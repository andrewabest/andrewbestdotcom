---
title: "Reflecting on Conventional"
date: 2019-09-13T15:00:00+09:30
draft: false
---

I have an open source .NET library which I maintain named [Conventional](https://github.com/andrewabest/conventional). 

It helps developers maintain standards within their codebases, doing things like making sure types have appropriate access modifiers on property accessors, making sure naughty things are not in use throughout a codebase (Looking at you `DateTime.Now`), and making sure types are located in sensible places - in amongst a whole host of other useful things. It works with your unit testing suite of choice.

Conventional relies heavily on .NET reflection to do its job. Most uses of Conventional look something like the following:

1. Using `Assembly`, get all of the accessible types within a given assembly
2. Using Linq, reduce this set down to a target set of interest - often locating types that implement a certain interface, `ICommand` for example, or that are located under a given namespace
3. Using Conventional and its built-in conventions, assert that the types meet a given convention or set of conventions
4. If there are any anomalies detected, fail the test and supply a list of the non-conforming types so that they might be corrected

All around awesome developer and ex-colleague of mine [George Kinsman](https://twitter.com/GeorgeKinsman) raised an [issue](https://github.com/andrewabest/Conventional/issues/62) on Conventional's GitHub repository recently. He had discovered something interesting: Conventional wasn't failing tests as it should have in certain scenarios.

The conventions in question look at method usage, using [Mono.Cecil](https://github.com/jbevain/cecil), within the types you supply Conventional. Conventional uses Cecil to inspect all of the lines of code (or `Instructions`) within method bodies in the supplied types, and looks for non-conforming code.

What George had discovered is that although lines of code in the supplied types clearly did not conform to the conventions, Conventional wasn't picking it up because the lines of code, once the code was compiled, _didn't actually exist in the supplied types_.

Two .NET features dynamically emit types at compile time that assist in enabling the features functionality: _async_, and _yield_. There is plenty of literature covering what each of those features do - we are more interested in understanding _how they compile_ here.

For an _async_ method like this:

```C#
public async Task<DateTime> GetDate()
{
	"Doing things".Dump();
	
	await Task.Delay(1);

	return DateTime.Now;
}
```

The corresponding emitted type looks like this:

```C#
[CompilerGenerated]
private sealed class <GetDate>d__1 : IAsyncStateMachine
{
    // Rest of type omitted for berevity

    private void MoveNext()
    {
        int num = <>1__state;
        DateTime now;
        try
        {
            TaskAwaiter awaiter;
            if (num != 0)
            {
                "Doing things".Dump();
                awaiter = Task.Delay(1).GetAwaiter();
                if (!awaiter.IsCompleted)
                {
                    num = (<>1__state = 0);
                    <>u__1 = awaiter;
                    <GetDate>d__1 stateMachine = this;
                    <>t__builder.AwaitUnsafeOnCompleted(ref awaiter, ref stateMachine);
                    return;
                }
            }
            else
            {
                awaiter = <>u__1;
                <>u__1 = default(TaskAwaiter);
                num = (<>1__state = -1);
            }
            awaiter.GetResult();
            now = DateTime.Now;
        }
        catch (Exception exception)
        {
            <>1__state = -2;
            <>t__builder.SetException(exception);
            return;
        }
        <>1__state = -2;
        <>t__builder.SetResult(now);
    }
}
```

There it is! Notice that the original method's instructions have _moved into_ the emitted type. That means they no longer exist in the declaring type. Which is why Conventional wasn't finding them! It needed to pull in and consider any compiler-generated _async_ types emitted during compilation, as they may contain code it needed to inspect!

It is a similar story with _yield_. For an iterator block like this:

```C#
public IEnumerable<DateTime> GetDates()
{
	"Doing things".Dump();
	
	yield return DateTime.Now;
}
```

The emitted type looks like this:

```C#
[CompilerGenerated]
private sealed class <GetDates>d__1 : IEnumerable<DateTime>, IEnumerable, IEnumerator<DateTime>, IDisposable, IEnumerator
{
    // Rest of type omitted for berevity

    private bool MoveNext()
    {
        switch (<>1__state)
        {
        default:
            return false;
        case 0:
            <>1__state = -1;
            "Doing things".Dump();
            <>2__current = DateTime.Now;
            <>1__state = 1;
            return true;
        case 1:
            <>1__state = -1;
            return false;
        }
    }
}
```

As with the _async_ variant, the original method's instructions have been moved into the emitted type.

The fix for both of the above cases was to identify the emitted types in question, pull them in, and inspect _their_ method instructions along with those of the supplied types, to ensure all source code lines are covered by the convention.

How do we find these emitted types? Luckily the .NET compiler leaves us a breadcrumb trail we can follow from the parent types to the emitted types, via two attributes that are applied during compilation: `AsyncStateMachineAttribute`, and `IteratorStateMachineAttribute`.

You can find _async_ emitted types like so:

```C#
type.ToTypeDefinition()
    .Methods
        .Where(x => x.HasAttribute<AsyncStateMachineAttribute>())
        .SelectMany(x => x.GetAsyncStateMachineType())
```

And you can identify _yield_ dynamic types like so:

```C#
type.ToTypeDefinition()
    .Methods
        .Where(x => x.HasAttribute<IteratorStateMachineAttribute>())
        .SelectMany(x => x.GetIteratorStateMachineType())
```

And with those types in hand, we can inspect _their_ contents - which means we can ensure all of the code we are intending to inspect with our conventions is actually inspected!

If you are an existing Conventional user, you might want to `Update-Package Best.Conventional` to ensure you are achieving the correct amount of code coverage. Version six also saw a set of breaking improvements focussed on property related conventions being more explicit. Happy testing!