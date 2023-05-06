<!--
.. title: Readlog: May 5, 2023
.. slug: readlog-may-5-2023
.. date: 2023-05-05 19:00:28 UTC+02:00
.. tags: de, kafka
.. category: 
.. link: 
.. description: 
.. type: text
-->

## Not Franz but still Kafka

Just read a few articles dedicated to Data Engineering and Apache Kafka:

1. [**Main tools in Data Engineering**](https://habr.com/ru/articles/727560/#habracut) <br>
  This is a great post on some of the most commonly used tools in Data 
  Engineering. It's short and to the point, with recommendations on where to 
  look to learn more.
  <!-- TEASER_END -->

2. [**How NOT to use Apache Kafka**](https://habr.com/ru/companies/cloud_mts/articles/725686/) <br>
  The article is certainly worth reading. Let me highlight the most important 
  point from there. Even though Kafka is a standard for wide data processing 
  of analytic and transactional data, it is still important to understand when 
  we should avoid using it. <br> <br>
    What Kafka is:
      * Scalable Platform of messages in real-time
      * Messaging Streaming Platform
      * Distributed Storage
      * Data Ingestion Framework
      * Data processing framework for continuous processing of stateless and stateful streams

    <br>

    What Kafka is NOT:

      * Proxy for millions of clients (like mobile apps)
      * API management platform
      * Data Base
      * IoT platform
      * A technology for strictly real-time applications such as 
      security-critical or deterministic systems <br><br>

    The article provides a lot of examples of proper and improper use. The author also references a lot of additional sources for controversial cases.
