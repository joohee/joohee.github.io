---
layout: post
title:  "Introduce AMCHARTS"
date:   2014-05-03 09:47:04
categories: javascript amchart
---

### amchart.js 소개 

chart library도 많고 많을 것인데, 그 중 팀에서 쓰고 있는 chart library가 있어서 소개해본다. 
http://www.amcharts.com/

수많은 demo와 예제들이 웹 사이트에서 제공되며, Pie 차트/Donut 차트/ Bar 차트 등 다양한 형태의 그래프를 제공해주고 있다. 세상은 넓고 유용한 툴들은 정말 널리고 널렸구나 싶다. 
그 중에서 정말 간단하게 사용한 Pie Chart 사용법에 대해 소개해본다. 

Javascript Libray이므로 일반 html 파일을 만들어서 테스트 가능하다. 
우선, 아래와 같이 필요한 library를 include 한다. 

	<script type="text/javascript" src="http://www.amcharts.com/lib/3/amcharts.js"></script>
	<script type="text/javascript" src="http://www.amcharts.com/lib/3/pie.js"></script>
	<script type="text/javascript" src="http://www.amcharts.com/lib/3/themes/none.js"></script>

다음, chart가 그려질 div태그와 스타일을 추가한다. 

    .#chartdiv {
        width		: 100%;
        height		: 500px;
        font-size	: 11px;
    }

	<div id="chartdiv"></div>
    
마지막으로 AmChart 객체를 이용하여 차트를 그려낸다. 
내용을 보면, 상당히 직관적이다. 아래 코드에서 dataProvider를 동적으로 생성해내면 원하는 차트 내용을 그려낼 수 있다. 

	var chart = AmCharts.makeChart("chartdiv", {
    "type": "pie",
	"theme": "none",
    "dataProvider": [
    {
            "country": "Lithuania",
            "litres": 501.9
        },
    {
            "country": "Czech Republic",
            "litres": 301.9
        }, {
            "country": "Ireland",
            "litres": 201.1
        }, {
            "country": "Germany",
            "litres": 165.8
        }, {
            "country": "Australia",
            "litres": 139.9
        }, {
            "country": "Austria",
            "litres": 128.3
        }, {
            "country": "UK",
            "litres": 99
        }, {
            "country": "Belgium",
            "litres": 60
        }, {
            "country": "The Netherlands",
            "litres": 50
        }],
        "valueField": "litres",
        "titleField": "country",
        "exportConfig":{	
          menuItems: [{
          icon: '/lib/3/images/export.png',
          format: 'png'	  
          }]  
        }
    });

이렇게 파일을 만들어서 웹 브라우저에 띄워보면 차트가 생성된다. 

위에서 언급한 예제는 아래 사이트에 잘 나와 있다. 
http://www.amcharts.com/demos/simple-pie-chart/

앞으로 프로젝트에서 사용한 툴/라이브러리를 하나씩 정리해나가려고 한다. 
화이팅!