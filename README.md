# customer-friendly-operator
This operator and application pattern is designed to allow for rolling restarts of Rails / Puma stacks on Kubernetes.

## Pattern

This system is primarily focused on AWS based services. As a result, services may be mentioned which are unavailable on other Cloud platforms such as Azure or GCE. Apologies, eventually I'll get around to adding those.

## Why is this a thing?

After running multiple Rails apps on Kubernetes with Puma as the primary application server, I've encountered practically every issue under the sun from running this at scale. One of the biggest issues in Kubernetes is the lack of built-in SIGNAL customisation.

[This issue](https://github.com/kubernetes/kubernetes/issues/24957) detailing the many issues companies face from the lack of an official way to send signals has existed since 2016.

## Okay, but why is sending SIGNALS to Puma so important? Doesn't Kubernetes have rolling restarts?
Here's the issue: **Puma has absolutely no idea how Kubernetes is handling requests**.

At a smaller scale, where you have a small number of pods and most of your requests happen during traditional business hours, you can easily get away with Kubernetes's rolling restart system. You'll probably drop one or two requests and, if you've designed things right, your frontend will retry them without any hassle.

At scale, you run the risk of cancelling important requests in-flight. These requests are customers making payments, multi-gb data imports, long-running report queries... Your core business. **You cannot afford to just YOLO your customer requests into the void and hope they'll retry them**.
