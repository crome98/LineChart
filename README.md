# LineChart
This is a simple line chart control

1- First, you need to clone the project and then build it.
2- Create your WPF project and then add new reference, which is the LineChart.dll (ouput of step 1) to it.
3- Add this to your project .XAML: xmlns:local="clr-namespace:LineChart;assembly=LineChart"
4- Now you can use this chart control by using: <local:LineChartLib/>
5- Currently, this chart control allows to set the following properties:

+ **Title**            : Chart's title (default "Title")
+ **GridlineColor**    : by default is set to "Gray"
+ **GridlinePattern**  : supported patterns: Solid (default), Dash, Dot and DashDot
+ **IsLegend**         : (true/false (default)) this boolean value is used to determine that legends need to be shown or not
+ **LegendPosition**   : if IsLegend is set to true, this property is used to determine the legends position. The following values are being supported 
  
  North,
  NorthWest,
  West,
  SouthWest,
  South,
  SouthEast,
  East,
  NorthEast (by default)
  
+ **IsXGrid**          : (true (default)/ false) this boolean value is used to determine that the horizontal gridlines are added to the chart or not
+ **XLabel**           : X axe's label     (default = "X Axis")
+ **Xmin**             : X axe's min value (default = 0.0)
+ **Xmax**             : X axe's max value (default = 10.0)
+ **XTick**            : X axe's step      (default = 2.0)

+ **IsYGrid**          : (true (default)/ false) this boolean value is used to determine that the vertical gridlines are added to the chart or not
+ **YLabel**           : Y axe's label   (default = "Y Axis")
+ **Ymin**             : Y axe's min value (default = 0.0)
+ **Ymax**             : Y axe's max value (default = 10.0)
+ **YTick**            : Y axe's step     (default = 2.0)

6- The following is the example:

**The .XAML file**
```
<Window x:Class="_2DChartDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="clr-namespace:LineChart;assembly=LineChart"
        Title="Window1" Height="350" Width="400" MinHeight="200" MinWidth="200">
    <Grid x:Name="rootGrid" SizeChanged="rootGrid_SizeChanged">
        <local:LineChartLib x:Name="myLineChart" Xmin="0" Xmax="20"
            XTick="1" Ymin="0" Ymax="10" YTick="0.5"
            Title="Data" GridlinePattern="Dash"/>
    </Grid>
</Window>
```

**The code behind it**

```
using System;
using System.Windows;
using System.Windows.Media;
using System.Threading;
using System.Collections.ObjectModel;
using System.Diagnostics;

namespace _2DChartDemo
{
    public partial class MainWindow : Window
    {
        private ObservableCollection<double> datum;
        public MainWindow()
        {
            InitializeComponent();
            datum = new ObservableCollection<double>(new double[21]);
            datum.CollectionChanged += Datum_CollectionChanged;
            Thread thread = new Thread(() => {
                while (true)
                {
                    Random rnd = new Random();
                    int number = rnd.Next(1, 10);
                    Dispatcher.Invoke(() =>
                    {
                        datum.RemoveAt(0);
                        datum.Add(number);
                    });
                    Thread.Sleep(1);
                }
            });
            thread.IsBackground = true; // Background thread will be destroyed when app is closed
            thread.Start();
        }

        private void Datum_CollectionChanged(object sender, System.Collections.Specialized.NotifyCollectionChangedEventArgs e)
        {
            try {
                switch (e.Action)
                {
                    // If new data is added to datum, redraw chart with new daa
                    case System.Collections.Specialized.NotifyCollectionChangedAction.Add:
                        AddDataToChart(datum);
                        myLineChart.AddChart();
                        break;
                }
            } catch (NotImplementedException ex) {
                Trace.WriteLine(ex.ToString());
            }
        }

        private void rootGrid_SizeChanged(object sender, SizeChangedEventArgs e)
        {
            if (rootGrid.ActualWidth >= 100 && rootGrid.ActualHeight >= 100) // Prevent invalid size change
            {
                myLineChart.Width = rootGrid.ActualWidth;
                myLineChart.Height = rootGrid.ActualHeight;
                AddDataToChart(datum);
            }
        }
        private void AddDataToChart(ObservableCollection<double> datum)
        {
            /*
              myLineChart.DataCollection.DataList holds all DataSeries (Polylines)
            */
            myLineChart.DataCollection.DataList.Clear();
            /* DataSeries is a curve, which is made from multiple Points, 
             * If we want to draw another curve, create another DataSeries with other data (Points)*/
            LineChart.DataSeries ds = new LineChart.DataSeries(); 
            ds.LineColor = Brushes.Blue;
            ds.LineThickness = 1;
            ds.SeriesName = "Received data";
            ds.Symbols.SymbolType = LineChart.Symbols.SymbolTypeEnum.Circle;
            ds.Symbols.BorderColor = ds.LineColor;
            for (int i = 0; i < datum.Count; i++)
            {
                double x = i;
                double y = datum[i];
                ds.LineSeries.Points.Add(new Point(x, y));
            }
            myLineChart.DataCollection.DataList.Add(ds);
            myLineChart.IsLegend = true;
            myLineChart.LegendPosition = LineChart.Legend.LegendPositionEnum.NorthEast;
        }
    }
}

```
                                            
![image](https://user-images.githubusercontent.com/25689764/166138899-d2190783-a06c-4df8-9efb-1528da66fe29.png)

                                            
Thank **Jack Xu** for his valuable knowledge.
**Author: LE HOANG PHUC**
