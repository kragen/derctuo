In a Kafka-like system running on a kernel where memory is transferred
rather than shared, the “commit log” for a channel could physically
consist of the uncopied message buffers the producers had transferred
to the broker.  With copy-on-write functionality, these message
buffers could be directly exposed to subscribers without ever copying
them, although at the risk of exposing subscribers to information
about the message bundle boundaries they are not supposed to depend on
(this risk is already present in Kafka).  With FlatBuffers and similar
techniques, publish-and-subscribe within a single CPU could proceed at
tens of gigabytes per second — billions of messages, hundreds of times
faster than ØMQ or Kafka, which are about equally fast.

Although, in such a high-bandwidth system, how do you limit retention?
