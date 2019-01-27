---
title: "The Unresolving Promise"
date: 2019-01-27T20:00:00+10:00
draft: false
---

I've been working on a personal project recently, that involves a set of services that are hosted in Kubernetes, that work together to accomplish facilitating a large number of people being invited to complete a submission of some sort, and managing the submission process. I'm sure Australian readers could hazard a guess at what I'm emulating.

Part of this project involves a client application, which I've chosen to build in Angular. 

The client application, as part of it operating securely with the APIs, needs to look for an invitation token on the query string of the route URL - if it finds this, it needs to attempt to exchange the invite token for a JWT. If it cannot find it it needs to redirect the user to an *Access Denied* page.

To facilitate the token exchange, a [Route Guard](https://angular.io/guide/router#milestone-5-route-guards) is implemented that checks to see if a user is authenticated, and if they aren't, checks for the existence of the token on the query string. If it finds this, it will redirect to a login component, whose responsibility it is to exchange the token for a JWT and redirect back to the original page being accessed.

To ensure a good user experience, we want the token exchange to run asynchronously, and to show some sort of loading spinner whilst it occurs. The first, naieve cut of code to perform this looks like so:

```TS
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import {HttpClient} from '@angular/common/http';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent implements OnInit {

  loading: boolean;
  constructor(private route: ActivatedRoute, private httpClient: HttpClient) {
    this.loading = true;
  }

  async ngOnInit() {
    const token = (await this.route.queryParamMap.toPromise()).get('token');
    await this.sleep(3000); // Exchange token for JWT
    this.loading = false;
  }

  sleep(ms: number = 0) {
    return new Promise(r => setTimeout(r, ms));
  }
}
```

Which drives a view which looks like this:

```html
<div>
    <img *ngIf="loading" src="https://loading.io/spinners/pacman/lg.eat-bean-pie-loading-gif.gif" />
    <p *ngIf="!loading">
      Success!
    </p>
</div>
```

Super basic stuff. Set a loading flag on construction, do some stuff asynchronously for a short amount of time, then reset the loading flag. *So why wasn't the loading spinner disappearing?*

After an amount of googling which revealed internet denizens pointing fingers vaguely at [Zone.js](https://github.com/angular/zone.js/), which provides angulars change tracking capabilities, and its patching of async APIs, I was stumped. Why wasn't my view updating?

I quickly shot a Slack message to [Tristan](https://github.com/tristanmenzel), one of Readify's reisdent Web gurus, to chat about the problem.  

His first inquest was to what was making the async call

> Angular uses Zone.js to detect async code and to run change detection afterwards.
> Everything in angular should be automatically wrapped in a zone.
> But if you've got something third party you might need to explicitly run it in a zone.

Nope, no third party code here, just my own simple TypeScript.

Next was to check the bindings were healthy.

> Try adding `<button (click)="loading = !loading">Toggle </button>` to the view.

That certainly had the desired toggling effect - the bindings are working in this case, as expected.

Third case under the microscope was the token retrieval mechanism

> Is your param subscription broken? Comment that out.

And just like that, _it now works_. 

But why is the token retrieval code not working?

```TS
const token = (await this.route.queryParamMap.toPromise()).get('token');
```

> I suspect your promise never resolved.
>
> As a promise I don't have a value, but I will eventually.
>
> An observable is I've maybe had values before you were here, I may have values after you leave or I may never have values.
>
> With observables you could do `Observable.fromEvent(buttonElement, 'click')` - if you never click the button you never get an event on the stream.
>
> So if you did `.toPromise()` on it, it never resolves.

This sounds like the likely culprit - my _promisified observable_ is not returning, and so my view is never getting updated. Not a fault of binding at all - and a good reminder for me not to think it is a [Zebra](https://en.wikipedia.org/wiki/Zebra_(medicine)) straight away.

On a related but different topic, given we are working with route parameters, and we are in an SPA, there is a good chance they may change whilst a component is alive, and we may want to _observe_ those changes. So whilst I want to verify that the above hypothesis is true, I'm also going to be better off using the `Observable` to... observe the potential changes to the route!

Verifying The Behaviour
===

First of all, let's attempt to subscribe to the Observable and see if we can update our view from the subscription.

```TS
async ngOnInit() {

    this.route.queryParamMap.subscribe(async p => {
        if (p.has('token')) {
        const token = p.get('token');
        // ... Go and exchange for our JWT
        this.loading = false;
        }
    });
}
```

That works! We are certainly recieving a value within our Observable - so why isn't our promise resolving?

To answer that question let's delve into the inner workings of the [RxJS toPromise](https://github.com/Reactive-Extensions/RxJS/blob/master/src/core/linq/observable/topromise.js) method. The key part we are interested in is the code which subscribes to the Observable, annoted below with the parameter names

```js
source.subscribe(/* onNext */ function (v) {
  value = v;
  }, 
  /* onError */ reject, 
  /* onComplete */ function () {
    resolve(value);
});
```

`onNext` is the function that is called every time a new value is emitted from the observable. In here we are just storing the last known value but not actually resolving the promise until we receive either an `error` or a `complete` event. 

What is this `complete` event? Going back to Observable basics, the lowest level way to create an Observable is through an `Observer` ([doc](http://reactivex.io/rxjs/class/es6/MiscJSDoc.js~ObserverDoc.html)). 

>  A well-behaved Observable will call an Observer's complete() method exactly once or the Observer's error(err) method exactly once, as the last notification delivered.

Now things are starting to make sense. Our params observable has no way of knowing when it has received its last value so it can only complete the observable when the component is being destroyed as you navigate to a different route. 

To confirm this behaviour, let's change our code and pipe our query params Observable into the [first](http://reactivex.io/documentation/operators/first.html) operator to give us a new Observabe which will complete as soon as it receives its first value. When that Observable completes it will resolve our Promise.

```TS
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import {HttpClient} from '@angular/common/http';
import { first } from 'rxjs/operators';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent implements OnInit {

  loading: boolean;
  constructor(private route: ActivatedRoute, private httpClient: HttpClient) {
    this.loading = true;
  }

  async ngOnInit() {

    const token = (await this.route.queryParamMap.pipe(first()).toPromise()).get('token');
    await this.sleep(3000);
    this.loading = false;
  }

  sleep(ms: number = 0) {
    return new Promise(r => setTimeout(r, ms));
  }
}
```

That works! Mystery solved.