![Visitor Count](https://profile-counter.glitch.me/4cesec/count.svg)![image](https://user-images.githubusercontent.com/80591549/193733756-318127aa-fa3e-4e85-bec6-d19b655e6092.png)  

<!-- 敲代码的图片 -->  
<div align="center" ><img order-radius="100px" src="https://cdn.jsdelivr.net/gh/sun0225SUN/photos/images/202108300019556.gif"/></div>  
<br>  



<!--
**4cesec/4cesec** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->

![Metrics](https://metrics.lecoq.io/4cesec?template=classic&isocalendar=1&base=header%2C%20activity%2C%20community%2C%20repositories%2C%20metadata&base.indepth=false&base.hireable=false&base.skip=false&isocalendar=false&isocalendar.duration=half-year&config.timezone=Asia%2FShanghai)  
![主席同志's Most used languages](https://github-readme-stats.vercel.app/api/top-langs?username=4cesec&show_icons=true&count_private=true&theme=gotham)  
[![主席同志's GitHub stats](https://github-readme-stats.vercel.app/api?username=4cesec&show_icons=true&theme=radical)](https://github.com/anuraghazra/github-readme-stats)  


<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>雷达图</title>
        <style>
            .container {
                margin: 30px auto;
                width: 600px;
                height: 300px;
                border: 1px solid #000;
            }
            .webs polygon,
            .lines line {
                fill: white;
                fill-opacity: 0.5;
                stroke: gray;
                stroke-dasharray: 10 5;
            }
            .webs polygon:nth-child(odd) {
                fill: lightgray;
            }
            .areas polygon {
                fill-opacity: 0.5;
                stroke-width: 3;
            }
            .areas circle {
                fill: white;
                stroke-width: 3;
            }
            .texts text {
                text-anchor: middle;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <svg width="100%" height="100%"></svg>
        </div>
        <script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
        <script>
            window.onload = function() {
                var width = 600, height = 300;
                // 创建一个分组用来组合要画的图表元素
                var main = d3.select('.container svg').append('g')
                        .classed('main', true)
                        .attr('transform', "translate(" + width/2 + ',' + height/2 + ')');
                // 模拟数据
                var data = {
                    fieldNames: ['语文','数学','外语','物理','化学','生物','政治','历史'],
                    values: [
                        [10,20,30,40,50,60,70,80]
                    ]
                };
                // 设定一些方便计算的常量
                var radius = 100,
                // 指标的个数，即fieldNames的长度
                        total = 8,
                // 需要将网轴分成几级，即网轴上从小到大有多少个正多边形
                        level = 4,
                // 网轴的范围，类似坐标轴
                        rangeMin = 0,
                        rangeMax = 100,
                        arc = 2 * Math.PI;
                // 每项指标所在的角度
                var onePiece = arc/total;
                // 计算网轴的正多边形的坐标
                var polygons = {
                    webs: [],
                    webPoints: []
                };
                for(var k=level;k>0;k--) {
                    var webs = '',
                            webPoints = [];
                    var r = radius/level * k;
                    for(var i=0;i<total;i++) {
                        var x = r * Math.sin(i * onePiece),
                                y = r * Math.cos(i * onePiece);
                        webs += x + ',' + y + ' ';
                        webPoints.push({
                            x: x,
                            y: y
                        });
                    }
                    polygons.webs.push(webs);
                    polygons.webPoints.push(webPoints);
                }
                // 绘制网轴
                var webs = main.append('g')
                        .classed('webs', true);
                webs.selectAll('polygon')
                        .data(polygons.webs)
                        .enter()
                        .append('polygon')
                        .attr('points', function(d) {
                            return d;
                        });
                // 添加纵轴
                var lines = main.append('g')
                        .classed('lines', true);
                lines.selectAll('line')
                        .data(polygons.webPoints[0])
                        .enter()
                        .append('line')
                        .attr('x1', 0)
                        .attr('y1', 0)
                        .attr('x2', function(d) {
                            return d.x;
                        })
                        .attr('y2', function(d) {
                            return d.y;
                        });
                // 计算雷达图表的坐标
                var areasData = [];
                var values = data.values;
                for(var i=0;i<values.length;i++) {
                    var value = values[i],
                            area = '',
                            points = [];
                    for(var k=0;k<total;k++) {
                        var r = radius * (value[k] - rangeMin)/(rangeMax - rangeMin);
                        var x = r * Math.sin(k * onePiece),
                                y = r * Math.cos(k * onePiece);
                        area += x + ',' + y + ' ';
                        points.push({
                            x: x,
                            y: y
                        })
                    }
                    areasData.push({
                        polygon: area,
                        points: points
                    });
                }
                // 添加g分组包含所有雷达图区域
                var areas = main.append('g')
                        .classed('areas', true);
                // 添加g分组用来包含一个雷达图区域下的多边形以及圆点
                areas.selectAll('g')
                        .data(areasData)
                        .enter()
                        .append('g')
                        .attr('class',function(d, i) {
                            return 'area' + (i+1);
                        });
                for(var i=0;i<areasData.length;i++) {
                    // 依次循环每个雷达图区域
                    var area = areas.select('.area' + (i+1)),
                            areaData = areasData[i];
                    // 绘制雷达图区域下的多边形
                    area.append('polygon')
                            .attr('points', areaData.polygon)
                            .attr('stroke', function(d, index) {
                                return getColor(i);
                            })
                            .attr('fill', function(d, index) {
                                return getColor(i);
                            });
                    // 绘制雷达图区域下的点
                    var circles = area.append('g')
                            .classed('circles', true);
                    circles.selectAll('circle')
                            .data(areaData.points)
                            .enter()
                            .append('circle')
                            .attr('cx', function(d) {
                                return d.x;
                            })
                            .attr('cy', function(d) {
                                return d.y;
                            })
                            .attr('r', 3)
                            .attr('stroke', function(d, index) {
                                return getColor(i);
                            });
                }
                // 计算文字标签坐标
                var textPoints = [];
                var textRadius = radius + 20;
                for(var i=0;i<total;i++) {
                    var x = textRadius * Math.sin(i * onePiece),
                            y = textRadius * Math.cos(i * onePiece);
                    textPoints.push({
                        x: x,
                        y: y
                    });
                }
                // 绘制文字标签
                var texts = main.append('g')
                        .classed('texts', true);
                texts.selectAll('text')
                        .data(textPoints)
                        .enter()
                        .append('text')
                        .attr('x', function(d) {
                            return d.x;
                        })
                        .attr('y', function(d) {
                            return d.y;
                        })
                        .text(function(d,i) {
                            return data.fieldNames[i];
                        });
            };
            function getColor(idx) {
                var palette = [
                    '#2ec7c9', '#b6a2de', '#5ab1ef', '#ffb980', '#d87a80',
                    '#8d98b3', '#e5cf0d', '#97b552', '#95706d', '#dc69aa',
                    '#07a2a4', '#9a7fd1', '#588dd5', '#f5994e', '#c05050',
                    '#59678c', '#c9ab00', '#7eb00a', '#6f5553', '#c14089'
                ]
                return palette[idx % palette.length];
            }
        </script>
    </body>
</html> 
