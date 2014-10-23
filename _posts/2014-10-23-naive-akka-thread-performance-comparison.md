---
layout: post
title: Naive Comparison of Akka Actor and Java Thread on Performance
tags:
  - java
  - akka
---

This is a naive comparison between Akka actor and Java thread, as far as the performance is concerned. 'Naive' means there's no complex logic or algorithm, but just a simple ping-pong program where two entities send messages to each other, in turns.

Although the performance tests are not thorough, nor they are real scenario, the comparison still gives a rough idea of Akka actor's performance.

<!--more-->

### Using Akka Actor

{% highlight scala %}
package pingpong

import akka.actor.{Props, ActorSystem, ActorLogging, Actor}

object PingPongActor {

  def main(args: Array[String]): Unit = {
    val system = ActorSystem("pingpong")
    val pinger = system.actorOf(Props[PingActor], "pinger")
    val ponger = system.actorOf(Props[PongActor], "ponger")
    pinger ! Ping(1)
  }
}

case class Ping(n: Int)

class PingActor extends Actor with ActorLogging {

  val ponger = context.actorSelection("akka://pingpong/user/ponger")

  override def receive = {
    case p: Ping =>
      ponger ! Ping(1)
  }
}

class PongActor extends Actor with ActorLogging {

  var start = -1L

  var count = 0L

  override def receive = {
    case p: Ping =>
      count += 1
      if (start < 0) {
        start = System.currentTimeMillis()
      }
      if (count % 100000 == 0) {
        val duration = System.currentTimeMillis() - start
        log.info("{}, {} seconds", count, duration / 1000)
      }
      sender ! Ping(1)
  }
}
{% endhighlight %}

### Using Thread

{% highlight scala %}
package pingpong

import java.util.concurrent.{ArrayBlockingQueue, BlockingQueue}
import org.slf4j.LoggerFactory

object PingPongThread extends App {
  val queue1 = new ArrayBlockingQueue[Int](1)
  val queue2 = new ArrayBlockingQueue[Int](1)
  val pinger = new Pinger(queue1, queue2)
  val ponger = new Ponger(queue2, queue1)
  pinger.start()
  ponger.start()
  queue1.put(1)
}

class Pinger(inQueue: BlockingQueue[Int], outQueue: BlockingQueue[Int]) extends Thread {

  override def run(): Unit = {
    while (true) {
      val o = inQueue.take()
      outQueue.put(1)
    }
  }
}

class Ponger(inQueue: BlockingQueue[Int], outQueue: BlockingQueue[Int]) extends Thread {

  val log = LoggerFactory.getLogger(this.getClass)

  var count = 0L

  var startTime = -1L

  override def run(): Unit = {
    while (true) {
      val o = inQueue.take()
      if (startTime < 0) {
        startTime = System.currentTimeMillis()
      }
      count += 1
      if (count % 10000 == 0) {
        val duration = System.currentTimeMillis() - startTime
        log.info("{} messages processed in {} seconds", count, (duration / 1000))
      }
      outQueue.put(1)
    }
  }
}
{% endhighlight %}

### Performance Comparison

 (number of messages per second)

| Actor  | Thread |
|--------|--------|
| 580,000|96,000  |

**Machine**

1. CPU: Intel(R) Core(TM) i3 M 370 @ 2.40GHz
1. Memory: 6 GB


