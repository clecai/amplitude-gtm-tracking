# amplitude-gtm-tracking

Do you want to implement Amplitude using Google Tag Manager (GTM)? You have knocked at the right door.

This repository contains json files and a few notes detailing the implementation of [Amplitude](https://amplitude.com/)  tracking through [Google Tag Manager or GTM](https://tagmanager.google.com/) as I initially intended it for the company I work for, [ManoMano](www.manomano.fr).

## Our approach to tracking
Presiding over our implementation of Amplitude through GTM, we have defined 4 guiding principles (AAAA) around what tracking should be :

### 1. Autonomy: 
A good tracking is a tracking that teams can maintain in autonomy, without undue dependances on other teams or individual contributors. At ManoMano we invested massively in educating the teams to this new approach to tracking and the tools and they should be autonomous. Tracking is not someone's job: it's everyone's.

### 2. Accessibility 
Related to the above, not only do we believe everyone should be responsible for tracking, we also want to make sure that *they are able to*. That means designing and giving trainings to everyone once, and then again and again to newbies. It also means that when choosing tools we will always go for the more intuitive options, the one that requires 1h of training and not 4. This has been a decisive factor for the choice of GTM and Amplitude.

### 3. Agility: 
In the world of Product Management, agility is much preached concept. We want our tracking to be just like our roadmap: to adapt to new challenges and changing priorities. We have accepted the fact that our tracking will never be exhaustive and that's OK. We also have provided our teams with a process that allow them to rapidly fill the gaps in their tracking with very short iterrations.

### 4. Agnosticity:
We want to build a tracking system that's above all passing trends and hot new tools. In our work we aim for agnosticity from products and solutions. Google Tag Manager and Amplitude appeared as the best joint solutions when we first designed our process but we want to remain free to migrate to whatever new tool the market offer us. When thinking about tracking, our hands should never be tied by the choices we made historically.

## About this repository
*The remainder of this documentation assumes you are somewhat familiar with Google Tag Manager (GTM) and Amplitude. If you are feeling iffy with GTM, I suggest you go ahead and watch the great video trainings of [MeasureSchool on YouTube](https://www.youtube.com/channel/UClgihdkPzNDtuoQy4xDw5mA). 
Before you start your implementation, you should feel comfortable with the dataLayer and the sequences of events happening when a webpage is loaded (pageview, DOM ready, window loaded).*

You can download *amplitude_gtm_tracking_container.json* and import it on GTM. It contains, by order of importance :
- `Amplitude — SDK`, a tag that is executed on All Pages (at pageview) and loads the necessary functions for Amplitude to work. 
- `amplitude_project_id`, a lookup variable, which contains the ids of the Amplitude projects we will be sending data to.
- `amplitude_event_properties`, a js object containing all the event-related variables that we will be sending to Amplitude.
- `amplitude_user_properties`, a js object containing all the user-related variables that we will be sending to Amplitude.
work.
- `Amplitude - event - TEMPLATE`, a tag with no trigger, which you should copy each time you want to add a new event. It contains the code that is executed each time you collect an event and send it to Amplitude. Note how it ends with a property (`'amp_pageview_recorded:true'`) being sent to the dataLayer. We will come back to this.
- `Amplitude - event - View page`, a tag triggered each time the window is loaded without the dataLayer containing the property `'amp_pageview_recorded:true'`.
- 4 other `Amplitude - event` tags, which collect data at specific stages of a classic e-commerce funnel.
- 5 triggers defining the stages of a classic e-commerce funnel (both server-side and client-side).
- 18 variables starting with `DL_`, which are dataLayer variables.

## Sidenote 1: understanding how Amplitude events work
Amplitude is an event-based tracking solution. Each event is basically an HTTP request that carries 4 informations: 
1. the event name
2. the user id
3. a json payload containing event-related variables
4. another json payload containing user-related variables

Purely in terms of data it works kind of like events on Google Analytics do, with the notable exception that **you can pass up to 2K variables to an event.** For us, it meant that, at first, we didn't need to worry about which variables should be sent to which events, we could basically send them all. This was step one in simplifying the process of adding new events to the tracking plan.

## Sidenote 2: Leveraging the dataLayer
You will notice that our implementation depends greatly on Google Tag Manager's dataLayer. Our aim is to have the data we collect transition through the dataLayer to GTM and feeding Amplitude with as few efforts as possible. This was step two of reducing friction in improving our tracking.

Most of the variables we collect — like `product_sku` or `product_price` — are updated at different stage of our funnel — when a product page is loaded or on an add-to-cart event on the category page for instance —, using the dataLayer we only need to connect them once for them to be sent to Amplitude.

## How it all fits together
A picture is worth a thousand words

## Collecting all pageviews without double-counting them

## Monitoring and QA-ing your tracking
foobar

## About Google Tag Manager
foobar
