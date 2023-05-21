---
layout: post
title: "Date in Spring"
date: 2023-05-22 01:10:00 +0900
tags: Spring
description: Spring에서의 Date(Modern and Legacy class in spring)
---

# Date

JavaScript에서의 Date는 대개 new Date(), dayJs, Intl 포맷으로 사용했으나 Spring boot에서 이전 자료들을 찾아보며
`java.util.Date`나 `java.sql.Timestamp`를 사용하다가 같이 일하는 사람의 코드에서 `java.time.Instant`를 사용하는것을 보고 차이점과 Date관련해서 찾아보았다.

![img]({{ '/assets/images/Date_Modern_Legacy.png' | relative_url }}){: .center-image }_출처: [stackoverflow, <What's the difference between Instant and LocalDateTime?>, 2022-09]_

위의 그림으로 알수 있듯 대표적으로 Timezone의 저장여부에서
~Local의 접미어가 판단된다.
즉, `Local~의경우 Timezone을 저장하지않는다`
또한 Instant는 정확한 UTC기준의 `순간`(아래부터 moment라 함)을 나타내지만, LocalDateTime은 `moment를 표현하지 못하고, 본질적으로 모호하다`

`Instant`는 1970 UTC부터의 nanoseconds를 나타낸다.

`ZoneOffset`은 UTC기준에서의 차이를 시간으로 나타낸다. (한국의 경우 UTC+9)

`ZoneId`는 흔히 시간을 나타낼떄 말하는 region을 나타낸다.
(예: Pacific/Auckland, America/Montreal)

`OffsetDateTime`은 ZoneOffset에 따른 moment를 나타내며 이때 시, 분, 초가 0일경우 Instant와 동일한 moment를 나타낸다.

`ZoneDateTime`은 `Instant(UTC) + ZoneId(region)`으로 표현되며 해당 Zone의 시간을 더한값이다.

{% highlight java %}

ZonedDateTime zdt = ZonedDateTime.now( z ) ;
// Pass a `ZoneId` object such as `ZoneId.of( "Europe/Paris" )`.

{% endhighlight %}

`local` LocalDateTime = LocalDate(YY-HH-DD) + LocalTime(hh-mm-ss)
Local에 대한것은 위와 같다.
Local은 region, timezone에 종속적이지 않다. 실제 zoneId를 찾는데 아무런 의미가 없다.

이 local은 특정한 local을 가리키는게 아닌 전체적 local, 모든 local을 뜻한다고 한다.

그렇기에 비즈니스적으로 상당히 특수한 경우가 아니면 사용안한다고한다.

1 여러 곳에 특정 날짜와 시간의 적용
예시로는 `크리스 마스의 시작이 25일 자정부터`임을 적용 시킬때로 말해주었다.
이의 시작은 Timezone에 따라 달라질수 있기에 공통되게 적용하기 위해 사용할 수 있다고한다.
{% highlight java %}
LocalDate ld = LocalDate.of( 2018 , Month.DECEMBER , 25 ) ;
LocalTime lt = LocalTime.MIN ; // 00:00:00
LocalDateTime ldt = LocalDateTime.of( ld , lt ) ; // Christmas morning anywhere.
{% endhighlight %}
2 예약
예시로는 `한곳에서 예약받는 상황`임을 적용시켜 얘기해주었다.
Timezone에따라 예약하는 시간이 변동될 수 있기 때문이다.
오후 1시를 예약하고 싶었지만 Timezone에따라 바뀔경우 1시부터 오차가 발생 가능한것을 말했다.

이러한 상황일경우 LocalDateTime과 ZoneId를 따로 저장하여
향후 LocalDateTime::atZone( ZoneId )로 ZonedDateTime을 생성하라고 안내해줬다.

{% highlight java %}

// Given a date, a time-of-day, and a time zone, determine a moment, a point on the timeline.
ZonedDateTime zdt = ldt.atZone( z ) ;
// Adjust from some zone to UTC. Same moment, same point on the timeline, different wall-clock time.
Instant instant = zdt.toInstant() ;

{% endhighlight %}
3 모르는곳
ZoneId가 특정되지 않는 모르는 지역에 대해 사용하는 경우가 있다고 한다. 하지만 이러한 대처는 좋은 데이터를 가지지 못하게 한다고 설명한다.(각 지역에 따른 통화 등의 데이터)

참고 자료.

[stackoverflow, <What's the difference between Instant and LocalDateTime?>, 2022-08-15],

[stackoverflow, <What's the difference between Instant and LocalDateTime?>, 2022-08-15]: https://stackoverflow.com/questions/32437550/whats-the-difference-between-instant-and-localdatetime/32443004#32443004
