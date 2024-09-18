

##### 1 Echarts

参考连接： https://github.com/hustcc/echarts-for-react
具体安装命令
```bash
npm install --save echarts-for-react

npm install --save echarts

```


``` ts
import React, { useEffect, useRef, useState } from 'react';  
import * as echarts from 'echarts/core';  
import {  
  TitleComponent,  
  TooltipComponent,  
  LegendComponent,  
} from 'echarts/components';  
import { PieChart } from 'echarts/charts';  
import { CanvasRenderer } from 'echarts/renderers';  
import { LabelLayout } from 'echarts/features';  
import { analysisUserInterfaceUsingGet } from "@/services/api-backend/analysisController";  
  
echarts.use([  
  TitleComponent,  
  TooltipComponent,  
  LegendComponent,  
  PieChart,  
  CanvasRenderer,  
  LabelLayout  
]);  
  
const PieChartComponent: React.FC = () => {  
  const chartRef = useRef<HTMLDivElement>(null);  
  const [chartData, setChartData] = useState<{ value: number; name: string }[]>([]); // 状态存储图表数据  
  
  useEffect(() => {  
    // 异步获取数据  
    const fetchData = async () => {  
      try {  
        const res = await analysisUserInterfaceUsingGet(); // 调用后端API获取数据  
        // @ts-ignore  
        const formattedData = res.data.map((item: { name: string, totalNum: number }) => ({  
          name: item.name,  // 将 source 映射为 name          
          alue: item.totalNum,  // 保留 value 字段  
        }));  

        setChartData(formattedData); // 
      } catch (error) {  
        console.error("Failed to fetch chart data:", error);  
      }  
    };  
  
    fetchData();  
  }, []); // 空数组作为依赖，确保只在组件挂载时执行一次  
  
  useEffect(() => {  
    const chartDom = chartRef.current;  
    if (chartDom && chartData.length > 0) {  
      const myChart = echarts.init(chartDom);  
  
      const option = {  
        title: {  
          text: '接口统计模板',  
          subtext: '统计那个接口调用的次数最多',  
          left: 'center'  
        },  
        tooltip: {  
          trigger: 'item'  
        },  
        legend: {  
          orient: 'vertical',  
          left: 'left'  
        },  
        series: [  
          {  
            name: 'Access From',  
            type: 'pie',  
            radius: '50%',  
            data: chartData, // 使用后端返回的数据  
            emphasis: {  
              itemStyle: {  
                shadowBlur: 10,  
                shadowOffsetX: 0,  
                shadowColor: 'rgba(0, 0, 0, 0.5)'  
              }  
            }  
          }  
        ]  
      };  
  
      myChart.setOption(option);  
  
      // Cleanup function to avoid memory leaks  
      return () => {  
        myChart.dispose();  
      };  
    }  
  }, [chartData]); // 当 chartData 改变时重新渲染图表  
  
  return (  
    <div  
      style={{  
        display: 'flex',  
        justifyContent: 'center',  
        alignItems: 'normal',  
        height: '100vh', // Full viewport height to center vertically  
      }}  
    >  
      <div ref={chartRef} style={{ width: '600px', height: '400px' }} />  
    </div>  );  
};  
  
export default PieChartComponent;
```



遍历数据吧数据进行遍历
```ts
   // 声明这个数据的格式
   const [chartData, setChartData] = useState<{ value: number; name: string }[]>([]);
   const formattedData = res.data.map((item: { name: string, totalNum: number }) => ({  
          name: item.name,  // 将 source 映射为 name          
          value: item.totalNum,  // 保留 value 字段  
      }));  
```