---
layout: post
title: jQuery=selector를 활용한 그룹표현
categories: 배움터-열공 일터-경험과노하우
tags: jQuery selector
date:   2014-07-12
---

표(table)는 데이터를 좀 더 이해하기 쉽고, 보기 좋게 해 주는 역할을 합니다. 하지만, 수십개의 데이터를 보게 된다면, 눈도 아프고, 복잡해 보이게 될 수도 있습니다. 그래서 ‘동적으로 데이터를 조건에 맞게 (날짜, 시간 등) 그룹을 지어 표현 해 줄 수 있다면 얼마나 좋을까? ‘라는 질문으로 시작하여 쉽게 접할 수 있는 jQuery, 그 중에서도 선택자(selecotr)를 활용해 구현을 해 보았습니다.

 
<br>
##1. 왜 그룹이 필요한가?  
우리게 표를 그리는 이유는, 수 없이 많은 데이터를 한눈에 쉽고 빠르게, 보고 분석하기 위해서 입니다. 아래  표에서 이상한 점을 발견 하셨습니까? 아래 표 처럼 모든 데이터를 아무런 정렬, 그룹 없이 있다면, 이 데이터를 이해하는데 시간이 필요하며, 오히려 있으나 마나한 쓸데 없는 데이터가 됩니다.

<br>
<p align="center"><img src= http://www.nextree.co.kr/wp-content/uploads/2014/06/dhsong_no_order_by_01.png></img></p>
<p align="center">**<그림1. 정렬, 그룹 적용 안된 표>**

처음 프로젝트를 시작 할 때에는 데이터의 양이 많이 않아 아무런 작업 없이 표현 하였습니다. 하지만, 양이 많아 짐에 따라, 복잡해지고, 빠르게 데이터를 볼 수 없었습니다. 그래서 정렬이 필요했고, 같은 데이터를 포함하고 있는 데이터 들은 그룹을 지어야 했습니다. 어떻게 개발을 해야할지 고민을 한 끝에 아래 처림 한눈에 데이터를 볼 수 있었고, 깔끔하게 표현 할 수 있었습니다.

<br>
<p align="center"><img src= http://www.nextree.co.kr/wp-content/uploads/2014/06/dhsong_rowspan_02.png></img></p>
<p align="center">**<그림2. 정렬, 그룹 적용 한 표>**

##2. 개발방법

그룹을 지으려면 먼저 데이터가 정렬이 되어있어야 했습니다. 모든 개발의 가장 기본인 데이터 정렬은 쿼리를 통해 해결 하였고, , jstl태그를 이용하여 표를 그렸습니다.  하지만 한 가지 문제점이 생겼습니다. jstl로만 작업을 한다면 적어도, 데이터의 갯수가 동일 하던지,  그룹지을 데이터가 몇개인지 알아야 하는 전제조건이 필요했습니다.

<br>
{% highlight python %}

    <table>
    <c:forEach items="${resultList} var="stat" varStatus="status">
        <tr id="accuracyStat_${stat.date}_${stat.timeId}_${status.index}">
            <!-- 그룹지을 데이터의 개수를 미리 알고 있다. 3개마다 rowspan을 적용하여 그룹을 표현한다. -->
            <c:if test=${status.index % 3 == 2>
               <td id="date_${stat.date}_${status.index}" rowspan="3"> ${stat.date} </td>
               <td id="day_${stat.date}_${status.index}" rowspan="3"> ${fn:parseKoreaDay(stat.date)}</td>
               <td id="time_${stat.date}_${stat.timeId}_${status.index}" rowspan="3">${stat.timeName} </td>
            </c:if>
            <td> ${stat.time}</td>
            <td> ${stat.count}</td>
        </tr>
    </c:forEach>
    </table>
{% endhighlight %}
<br>

하지만,  데이터 특성상 일 /시간별 데이터의 개수가 다르기 때문에 미리 몇개씩 그룹을 지을지 예측 할 수 없는 상황이었습니다.  그래서 jQuery를 사용하여 동적으로 데이터의 개수를 확인하고 그에 따른 rowspan을 적용을 해야 겠다고 생각했습니다.

그래서  각 <td> 태그에 고유한 식별자(ID)를 부여하여, jQuery 선택자를 통하여 접근 할 수 있게 했습니다. id는 날짜(yyyyMMdd) , 시간 식별자(time Id), 리스트 인덱스(index)를 언더바( _ )로 구분하여, 정규표현식을 사용한 선택자를 통하여 여러개의 object를 선택할수있게 하였습니다.

<br>
{% highlight python %}

    <table>
    <c:forEach items="${resultList} var="stat" varStatus="status">
        <tr id="accuracyStat_${stat.date}_${stat.timeId}_${status.index}">
            <!-- date_날짜_리스트 인덱스 -->
            <td id="date_${stat.date}_${status.index}"> ${stat.date} </td>

            <!-- day_날짜_리스트 인덱스 -->
            <td id="day_${stat.date}_${status.index}"> ${fn:parseKoreaDay(stat.date)}</td>

            <!-- time_날짜_시간 아이디_리스트 인덱스 -->
            <td id="time_${stat.date}_${stat.timeId}_${status.index}">${stat.timeName} </td>

            <td> ${stat.time}</td>
            <td> ${stat.count}</td>
        </tr>
    </c:forEach>
    </table>
{% endhighlight %}
<br>

가장 먼저 했던 것은 날짜와 요일로 그룹을 짓기 위해 목록에서 날짜 리스트를 가져오는 것 이었습니다. 이 날짜 목록은 중복이 제거된 유니크한 데이터만 들어있는 리스트이어야 하고, 이 리스트를 통하여 해당 하는 날짜의 데이터가 몇개가 있는지 확인 해야 합니다. 그래서 정규표현식을 사용하여  id가 “request_”로 시작하는 모든 <td>의 날짜를 가져와 그 해당하는 날짜가 리스트에 있는지 확인합니다.

	 $(‘td[id^="requestDate_"]‘)……

<br>
{% highlight python %}

    // 유니크한 날짜가 들어있을 배열
    function getUniqueDate () {
         var uniqueDate = [];
         $('td[id^="requestDate_"]').each(function(i, data) {
             // td태그 속에 있는 text(날짜)를 가져온다.
             var requestDate = $(data).text();

             // 배열속에 해당 날짜가 있는지 확인하고, 없다면 배열에 추가
             if($.inArray(requestDate, uniqueDate) === -1) uniqueDate.push(requestDate);
         });
     };
{% endhighlight %}
<br>

이제  날짜 리스트를 통하여 각 날짜 컬럼에 rowspan을 얼마나 적용할 지 알 수 있습니다.

<br>
{% highlight python %}

    // 중복없는 유니크한 날짜 목록
    var uniqueDate = getUniqueDate();

    $.each(uniqueDate, function(i, date) {

        // date 별로 rowspan을 적용하기 위해 개수를 파악한다.
        var $requestDates = $('td[id^="date_'+date+'"]');
        $requestDates.not(":first").remove();
        // date td 개수 만큼 rowspan 속성 적용
        $requestDate.attr("rowspan", $requestDates.size());

        // day 별로 rowspan을 적용하기 위해 개수를 파악한다.
        var $requestDays = $('td[id^="day_'+date+'"]');
        $requestDays.not(":first").remove();
        // day td 개수만큼 rowspan 속성 적용
        $requestDays.attr("rowspan", $requestDays.size());

        // 컨트롤러에서 넘겨온 시간리스트의 사이즈를 구한다.
        // 리스트에는 [아침, 점심 등 사용자가 정의한 시간의 목록이 들어있다.
        // 예시는 아침의 리스트만 들어있다.
        var timeSize = ${fn:length(timeList)};
        for (var j = 0; j <= timeSize; j++ ) {
            var $requestTimes = $('td[id^="requestTime_'+date+'_'+ j +'"]');
            $requestTimes.not(":first").remove();
            $requestTimes.attr("rowspan", $requestTimes.size());
        }
    }
{% endhighlight %}
<br>

여기에서 중요한 점은 그룹지을 <td> 에 rowspan 속성을 적용하려면 첫번째를 제외한 나머지 <td>는 제거 되야합니다. 그래서 자식 선택자를 사용하여 첫번째를 제외한 나머지를 지우게 되었습니다.

	$requestDates.not(“:first”).remove();

위 소스의 뜻은 ‘ $requestDates’ 목록에서 첫번째(“:first”)가 아닌(not) 것을 지워라(remove)’ 라는 의미 입니다. 위 코드를 적용하면, rowspan을 적용할 <td>하나만 남고 나머지는 지워지게 되고, rowspan 속성이 적용되어 그룹을 짖게 됩니다. 이와 같이 날짜, 시간도 같은 방법을 적용을 하면 동적으로 rowspan 속성을 적용 할 수 있습니다.

<br>
<p align="center"><img src= http://www.nextree.co.kr/wp-content/uploads/2014/06/dhsong_03_2.png</img></p>


위 소스코드를 적용을 하면, 위 그림에 회색영영의 <td> 태그들이 제거가 되며, 붉은색 영영의 <td>에는 rowspan 속성이 적용이 되어 그룹지어진 테이블을 표현 할 수 있습니다.

##3. 마치며

저는 이번 개발을 하면서 자주 사용해서 익숙했던 jQuery 정규표현식 / 자식선택자를 사용했습니다. 하지만 사용하는 법보다 어떻게 활용을 할지가 더 어려웠던 순간 이었습니다. 다른 사람들이 했던 예제를 찾아보거나, 책을 찾아 문제를 해결 했습니다. 비록 기초적인 코드지만, 문제를 해결하는데 있어서 중요한 역할을 했습니다.
이번 작업을 하면서 한가지 아쉬웠던점은 플러그인으로 만들지 못 해 어디에서나 사용하게 끔 개발을 하지 못 한 점입니다. 만약 다른 테이블에도 적용을 하게 만들으려면 추가적으로 소스를 수정, 추가를 해야하는 경우가 발생 할 것입니다. 추후에 이와 같이 다양하게 사용하는 코드를 작성한다면, 좀 더 넓게 생각하여 누구나, 다양하게 사용 할 수 있는 코드를 만들고 싶습니다.
<br>  


---

*Posted by 송대호*
