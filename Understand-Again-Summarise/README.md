
# Understand-Again-Summarise

List of some random notes, links, videos which can be directly visited and read (or) stuff which I have to revisit again and summarise in the repo: 
- Misc:
  - https://unix.stackexchange.com/questions/727101/why-do-processes-on-linux-crash-if-they-use-a-lot-of-memory-yet-still-less-than
  - https://www.chiark.greenend.org.uk/~sgtatham/quasiblog/coroutines-philosophy/
  - Go memory model: https://go.dev/ref/mem
  - https://news.ycombinator.com/
  - https://sachidisanayaka98.medium.com/how-chrome-browser-use-process-threads-643dff8ad32c
  - https://www.linkedin.com/posts/hnaser_in-the-beginning-for-the-os-to-write-to-activity-7163388923916861441-t1vw?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_the-big-win-of-using-threads-instead-of-processes-activity-7161147178546069506-yehp?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_fragmentation-is-a-very-interesting-topic-activity-7156142414989037568-6C96?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_today-i-learned-how-the-linux-option-netipv4-activity-7150555792662740992-w8fL?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_i-just-learned-that-in-addition-to-the-mapping-activity-7148454941404110848-8m4D?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_i-am-fascinated-by-gos-compiler-escape-analysis-activity-7144747978224746496-z-YZ?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_glad-mongo-fixed-this-in-62-so-prior-to-activity-7135553971066175489-nwm7?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_why-does-it-take-time-for-dns-to-resolve-activity-7134793549526528001-xjL0?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_a-connection-pool-is-always-a-good-idea-especially-activity-7134109245909725184-qaUE?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_graphql-was-invented-by-facebook-mainly-because-activity-7127490321701056513-DSxX?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_the-recent-cloudflare-api-outage-on-november-activity-7126989541537677312-upGW?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_http3-is-taking-over-the-world-but-consider-activity-7116186211039285248-Bae7?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_i-got-asked-how-vpn-works-on-x-so-here-is-activity-7110641803984322560--ONA?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_fun-networking-fact-http-related-pglocks-activity-7108275178979160064-gAVu?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_its-fascinating-to-know-how-jit-just-in-activity-7101992901496229888-_777?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_postgres-has-weak-locks-those-are-table-activity-7078250396678303744-p6WU?utm_source=share&utm_medium=member_desktop
  - https://www.linkedin.com/posts/hnaser_normally-when-you-write-to-disk-the-writes-activity-7067253338395852800-r2JY?utm_source=share&utm_medium=member_desktop
  - https://bugs.mysql.com/bug.php?id=109595
  - https://www.youtube.com/watch?v=lCb5BkJOOVI&list=PLQnljOFTspQU0ICDe-cL1EwXC4GDSayKY&index=43
  - https://medium.com/@hnasr/the-journey-of-a-request-to-the-backend-c3de704de223
  - https://blog.jcole.us/2014/04/16/the-basics-of-the-innodb-undo-logging-and-history-system/
  - https://medium.com/@hnasr/how-slow-is-select-8d4308ca1f0c
  - https://medium.com/@hnasr/what-happens-when-databases-crash-74540fd97ea9
  - https://www.linkedin.com/pulse/how-troubleshoot-long-postgres-startup-nikolay-samokhvalov/
  - https://keefmck.blogspot.com/2023/04/why-ssds-lie-about-flush.html?m=1
  - https://tontinton.com/posts/scheduling-internals/
  - https://stackoverflow.com/questions/1518711/how-does-free-know-how-much-to-free
  - https://blog.allegro.tech/2024/03/kafka-performance-analysis.html
  - https://www.youtube.com/watch?v=d86ws7mQYIg
  - https://www.linkedin.com/pulse/builder-design-pattern-prateek-mishra
  - https://www.reddit.com/r/docker/comments/1f1wqnb/how_i_reduced_docker_image_size_from_588_mb_to/
  - https://anyesh.medium.com/be-careful-while-using-lru-cache-on-the-method-a3dc2053748b
  - https://docs.eucalyptus.cloud/eucalyptus/4.4.6/user-guide/monitoring_concepts.html
    - A statistic is computed aggregation of metric data over a specified period of time. CloudWatch provides statistics based on the metric data points you or Eucalyptus provide. Aggregations are made using the namespace, metric name, dimensions, and the data point unit of measure, within the time period you specify. The following table describes the available statistics.
  
| **Statistic**   | **Description**                                                                                                                                   |
|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| **Minimum**      | The lowest value observed during the specified period. You can use this value to determine low volumes of activity for your application.         |
| **Maximum**      | The highest value observed during the specified period. You can use this value to determine high volumes of activity for your application.       |
| **Sum**          | All values submitted for the matching metric added together. You can use this statistic for determining the total volume of a metric.            |
| **Average**      | The value of `Sum / SampleCount` during the specified period. By comparing this statistic with the Minimum and Maximum, you can determine the full scope of a metric and how close the average use is to the Minimum and Maximum. This comparison helps you to know when to increase or decrease your resources as needed. |
| **SampleCount**  | The count (number) of data points used for the statistical calculation.                                                                          |

  - Difference between DynamoDb PutItem vs UpdateItem: PutItem will Replace an entire item while UpdateItem will Update it.
  - Youtube/Linkedin/Twitter articles: Alex Xu, Arpit Bhayani, Hussaein Nasser - to watch/update
  - Code coverage is calculated by dividing the number of lines of code executed by a test suite by the total number of lines of code in an application, and then multiplying by 100: https://stackoverflow.com/questions/195008/what-is-code-coverage-and-how-do-you-measure-it
  - About Pool Sizing: https://github.com/brettwooldridge/HikariCP/wiki/About-Pool-Sizing
  - FSImage in Hadoop?
- Short notes from any book/research paper I'm reading: (To be updated in repo)
  - Dynamodb...
  - 
 

----------------------------------------------------------------------

