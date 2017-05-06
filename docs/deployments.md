## Deployments

The running instantiation of a service is called a deployment. Deployments actively process
events from a topic and update aggregates, which may be queried from HTTP endpoints. Deployments
run in a fully-hosted setting in Pyroclast's cloud.

### Activating / Upgrading

After designing a service, the next thing you'll want to do is launch it. Choose "Commit" in the
upper right hand corner of the editing screen, then click on the Deployments bar the bottom oft
the screen. This will drop up a list of deployments that can be activate or upgrade. Before a deployment
can take place, the service must be committed, and must be in a valid state. If the service is invalid
or it's not been committed, the buttons will be disabled.

Choose "activate" (if this is the first time this deployment will run) or "upgrade" (available if this
deployment had already been started). A modal will pop up confirming that you are clear for launch. You
may either *rebuild* state, or *resume* from the last place this deployment left off - depending on the circumstances.

#### Rebuild

A deployment can always launch and *rebuild* its state, representing aggregates, from scratch. Rebuilding will begin
this deployment at the very first event the input topic has received. You'd usually choose this option if you're
making a correct to an existing service that had been deployed.

#### Resume

The other option that can be chosen is to *resume* this deployment. Resuming will pick back up from the last
point where this deployment stopped processing. Pyroclast will only let you resume when it's safe to do so.
This button will be disabled if you changed any property that would cause an aggregate to be computed in a different
way that would make it inconsistent with how it previously operated. This option is handy for avoiding recomputing
the entire stream of records in a topic when picking back up from the last record would work just as well.

### Endpoints

So far, we've covered how to make a topic to ingest events, how to design a service that will change those events
in some way, and how to launch that service. Endpoints are the means by which data is extracted from Pyroclast.
Each aggregation in a service will receive an endpoint, such that when a deployment for that service is launched,
invoking the endpoint will return the contents of the aggregate. This is similar to querying a database, except
that the results to this sort of query are continuously computed and constantly available. Each aggregate gets
its own endpoint, and endpoints are "stable" for a deployment. That means repeatedly deploying the production build
of a service with an aggregate will maintain the same endpoint information, and downstream consumers who invoke
that endpoint won't need to change anything.

### Exceptions

Sometimes services will try to apply a function to an event in an incompatible way, and an exception will be thrown.
For example, you might choose the Plus function to add 50 the value in key "score" of the event. If the event doesn't
have a value for that key, an exception will be thrown. Exceptions do not stop the deployment from proceeding. Each
exception (up to 100) will be logged on the right side of the deployment screen, denoting the type of exception, the event
that caused the exception, and it's stacktrace. This information can be used to debug what went wrong, and you might modify
and rebuild your deployment in response.

### Metrics

Each running deployment maintains a set of metrics about how many events are being processed, and how latent they are.
Pyroclast exposes the amount of records being received in the input step per second, the amount of records either
being aggregated or output per second, and the 90th percentile of latency in milliseconds that it took each event
to fully makes its way through all the steps.
