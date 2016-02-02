Celery is a distributed task system.


Task
----
Every task must have a unique name, and a new name will be generated out of the function name if a custom name is not provided.


Broker
------


Worker
------


Event
-----
The worker has the ability to send a message whenever some event happens. These events are then captured by tools like Flower, and **celery events** to monitor the cluster.

Heartbeat
---------


http://stackoverflow.com/questions/14817181/django-celery-connectionerror-too-many-heartbeats-missed



FAQ
---
Why say celery is a distributed task system?
