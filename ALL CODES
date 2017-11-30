using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
//using System.ComponentModel;
using System.IO.Ports;
using System.Threading;
//using System.Threading.Tasks;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;

namespace UpperComputerForSTM32
{
#region CLASS DATADEAL
    public class DataDeal
    {
        SerialPort sp=null;
        string sendData;
        string messageBoxTitle = "Message";
        MessageBoxImage messageBoxImageWarning = MessageBoxImage.Warning;
        MessageBoxButton messageBoxButton = MessageBoxButton.OK;
        MessageBoxImage messageBoxImage = MessageBoxImage.Error;
        #region FUNCTION CONSTRUCTOR
        public DataDeal(SerialPort sp,string sendData)
        {
            this.sp = sp;
            this.sendData = sendData;
        }
        #endregion
        #region FUNCTION SEND DATA
        public void DataSend_isHex()
        {
            try
            {
                if (sp.IsOpen && (sendData != "") && (sp != null))
                {
                    // sp.DiscardOutBuffer();
                    sendData = sendData.Replace("0x", "");                     //去掉0x,0X字符串;
                    sendData = sendData.Replace("0X", "");
                    sendData = sendData.Replace(" ", "");                         //去掉空格；
                    byte[] sendBytes = new byte[sendData.Length / 2];
                    int j = 0;
                    for (int i = 0; i < sendData.Length; i += 2)
                    {
                        sendBytes[j++] = Convert.ToByte(Int32.Parse(sendData.Substring(i, 2), System.Globalization.NumberStyles.HexNumber));  //将输入的十六进制String转化为Byte数组；
                    }
                    sp.Write(sendBytes, 0, sendData.Length / 2);
                    MessageBox.Show("Send Successfully!\n", messageBoxTitle, messageBoxButton);
                }
                else if (sp.IsOpen == false)
                {
                    MessageBox.Show("Please Open The Port!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
                }
                else
                {
                    MessageBox.Show("Please Input The Sending Data!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
                }
            }
            catch (Exception)
            {
                MessageBox.Show("Unable To Send Data!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
            }
        }
        public void DataSend_notHex()
        {
            try
            {
                if ((sp.IsOpen) && (sendData != ""))                   //Send Data;
                {
                    try
                    {
                        sp.DiscardOutBuffer();
                        sp.WriteLine(sendData);
                        MessageBox.Show("Successfully Send!\n", messageBoxTitle, messageBoxButton);
                        //       System.Threading.Thread.Sleep(10);
                        //DataRecieveBox.Text= sp.ReadLine();
                    }
                    catch (Exception)
                    {
                        MessageBox.Show("Send Failed!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
                        return;
                    }
                }
                else if (sendData == "")                                       //Data is empty;
                {
                    MessageBox.Show("Illegal Sending Data. Input Again!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
                }
                else
                {
                    MessageBox.Show("Port Is Not Opened Normally!\n", messageBoxTitle, messageBoxButton, messageBoxImageWarning);
                }
            }
            catch (Exception)
            {
                MessageBox.Show("Send Failed!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
            }
        }
#endregion
    }
#endregion

    /// <summary>
    /// The Upper Computer For STM32
    /// </summary>
    #region CLASS MAINWINDOW
    public partial class MainWindow : Window
    {
        #region VERIABLE DEFINITION
        string messageBoxTitle = "Message";
        string recvDataStore = null;
        MessageBoxButton messageBoxButton = MessageBoxButton.OK;
        MessageBoxImage messageBoxImage = MessageBoxImage.Error;
        MessageBoxImage messageBoxImageWarning = MessageBoxImage.Warning;
        SerialPort sp=new SerialPort() ;
        bool isOpen = false;
        bool isRecieveHex = false;
        bool isSendHex = false;
        bool canRead = false;               //标记是否可读取recvDataStore
        bool isQuery = false;                   //标记是否是查询引起的数据接收
     //   string sendData;
        #endregion
        #region FUNCTION MainWindow
        public MainWindow()
        {
            InitializeComponent();
            ComboBoxInitialize();
        }
        #endregion
        #region FUNCTION COMBO BOX INITIALIZE
        public void ComboBoxInitialize()
        {
            //BaudRateSelection Initialize;
            BaudRateSelection.Items.Add("1200");
            BaudRateSelection.Items.Add("2400");
            BaudRateSelection.Items.Add("4800");
            BaudRateSelection.Items.Add("9600");
            BaudRateSelection.Items.Add("19200");
            BaudRateSelection.Items.Add("38400");
            BaudRateSelection.Items.Add("43000");
            BaudRateSelection.Items.Add("56000");
            BaudRateSelection.Items.Add("57600");
            BaudRateSelection.Items.Add("115200");
            BaudRateSelection.SelectedIndex = 3;
            //ComPortSelection Initialize;
            for (int i = 0; i < 10; i++)
            {
                ComPortSelection.Items.Add("COM" + (i + 1).ToString());
            }
            ComPortSelection.SelectedIndex = 0;
        }
        #endregion
        #region FUNCTION CHECK PORT SETTINGS
        public bool PortSettingCheck()
        {
            if (BaudRateSelection.Text.Trim() == "") return false;
            if (ComPortSelection.Text.Trim() == "") return false;
            return true;
        }
        #endregion
        #region FUNCTION FIND VALID PORT
        private void PortCheckButton_Click(object sender, RoutedEventArgs e)
        {
            bool ComAvalible = false;
            ComPortSelection.Items.Clear();
            for (int i = 0; i < 10; i++)                      //Check The avaliable port;
            {
                try
                {
                    SerialPort sp = new SerialPort("COM" + (i + 1).ToString());
                    sp.Open();
                    sp.Close();
                    ComPortSelection.Items.Add("COM" + (i + 1).ToString());                        //Add avaliable port number;
                    ComAvalible = true;
                }
                catch (Exception)
                {
                    continue;
                }
            }
            if (ComAvalible)               //重置COM口编号
            {
                ComPortSelection.SelectedIndex = 0;
            }
            else
            {
                MessageBox.Show("No Avaliable Port!\n", messageBoxTitle, messageBoxButton, messageBoxImage); //扔出一个报警，没有可用端口
            }
        }
        #endregion
        #region FUNCTION OPEN PORT
        private void PortOpenButton_Click(object sender, RoutedEventArgs e)
        {
            if (PortSettingCheck() & (!(isOpen)))           //端口检查通过并且没有被打开过；
            {
              //  sp = new SerialPort();
                try
                {
                    sp.PortName = ComPortSelection.Text.Trim();
                    sp.BaudRate = Convert.ToInt32(BaudRateSelection.Text.Trim());
                    sp.StopBits = StopBits.One;
                    sp.Parity = Parity.None;
                    sp.DataBits = 8;
                    sp.ReadTimeout = -1;
                    sp.Open();
                    sp.RtsEnable = true;
                   // sp.DataReceived += new SerialDataReceivedEventHandler(sp_DataReceived);
                    isOpen = true;
                    BaudRateSelection.IsEnabled = false;
                    ComPortSelection.IsEnabled = false;
                    HexRecieveCheckBox.IsEnabled = false;
                    HexSendCheckBox.IsEnabled = false;
                    sp.DiscardInBuffer();
                    sp.DataReceived += new SerialDataReceivedEventHandler(sp_DataReceived);
                    MessageBox.Show("Port Open Successfully!", messageBoxTitle, messageBoxButton);
                }
                catch (Exception)
                {
                    MessageBox.Show("Illegal Port Settings!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
                    return;
                }
            }
            else
            {
                if (isOpen) MessageBox.Show("This Port Has Been Opened, No Need To Open Again!", messageBoxTitle, messageBoxButton, messageBoxImageWarning);
                else
                    MessageBox.Show("Ilegal Port Settings!", messageBoxTitle, messageBoxButton, messageBoxImage);
            }
        }
        #endregion
        #region FUNCTION CLOSE PORT
        private void PortCloseButton_Click(object sender, RoutedEventArgs e)
        {
            if ((isOpen == true) && (sp != null))
            {
                try
                {
                    sp.Close();
                    ComPortSelection.IsEnabled = true;
                    BaudRateSelection.IsEnabled = true;
                    HexRecieveCheckBox.IsEnabled = true;
                    HexSendCheckBox.IsEnabled = true;
                    isOpen = false;
                }
                catch (Exception)
                {
                    ComPortSelection.IsEnabled = false;
                    BaudRateSelection.IsEnabled = false;
                    HexRecieveCheckBox.IsEnabled = false;
                    HexSendCheckBox.IsEnabled = false;
                    isOpen = true;
                    MessageBox.Show("Cannot Close This Port!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
                }
            }
            else if (sp == null)
            {
                MessageBox.Show("No Port To Close!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
            }
            else
            {
                MessageBox.Show("The Port Has Been Closed, No Need to Close Again!\n", messageBoxTitle, messageBoxButton, messageBoxImageWarning);
            }
        }
        #endregion
        #region FUNCTION SELECT BAUD RATE
        private void baudRate_SelectionChanged(object sender, SelectionChangedEventArgs e)
        {
            //  MessageBox.Show("Content=" + BaudRateSelection.Text.Trim());
        }
        #endregion
        #region FUNCTION  SELECT PORT
        private void comPort_SelectionChanged(object sender, SelectionChangedEventArgs e)
        {

        }
        #endregion
        #region FUNCTION SEND DATA
        private void DataSendButton_Click(object sender, RoutedEventArgs e)
        {
            if (isSendHex== false)
            {
                //    sendData = DataSendBox.Text;
                //   dataSend_notHex();                                                       //调用数据处理类来发送数据；
                DataDeal sendData_notHex = new DataDeal(sp, DataSendBox.Text);
                sendData_notHex.DataSend_notHex();
            }
            else
            {
                // sendData = DataSendBox.Text;
                //  dataSend_isHex();
                DataDeal sendData_isHex = new DataDeal(sp, DataSendBox.Text);
                sendData_isHex.DataSend_isHex();
            }
        }
        #endregion
        #region FUNCTION SENDING DATA CLEAR
        private void SendingDataClearButton_Click(object sender, RoutedEventArgs e)
        {
            DataSendBox.Text = "";
        }
        #endregion
        #region FUNCTION RECIEVED DATA CLEAR
        private void RecievedDataClearButton_Click(object sender, RoutedEventArgs e)
        {
            DataRecieveBox.Text = "";
        }
        #endregion
        #region FUNCTION QUERY SPEED
        private void SpeedQureyButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                //sendData = "0x5504aa";
                // dataSend_isHex();
                DataDeal speedQurey = new DataDeal(sp, "0x5504aa");
                speedQurey.DataSend_isHex();
                isQuery = true;
                //System.Threading.Thread.Sleep(200);  //延时200ms等待数据的发送；
                for (long i = 0; i < 10000000; i++)                   //做个延时，等待数据接收完成；
                {
                    if (canRead)
                        break;
                }
                if (!canRead)
                {
                    MessageBox.Show("Failed To Query!");
                    return;
                }
                else
                {
                    string speed;
                    speed = recvDataStore;
                    if (speed.Substring(2, 2) == "55" && speed.Substring(speed.Length - 2) == "aa")      //判断是否是合法接收字符串
                    {
                        speed = speed.Replace("0x", "");
                        speed = speed.Replace("0X", "");
                        speed.Replace("aa", "");
                        int speedNum = Int32.Parse(speed, System.Globalization.NumberStyles.HexNumber);
                        speed = speedNum.ToString();
                        SpeedOutput.Value = speed;
                        SpeedResult.Text = speed;
                    }
                    else
                    {
                        MessageBox.Show("Illegal Temperature Message", messageBoxTitle, messageBoxButton, messageBoxImage);
                        isQuery = false;
                        canRead = false;
                        return;
                    }
                    isQuery = false;
                    canRead = false;

                }
            }
            catch (Exception)
            {
                MessageBox.Show("ERRO!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
            }
        }
        #endregion
        #region FUNCTION QUERY HUMIDITY
        private void HumidityQueryButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                //sendData = "0x5502aa";
                //  dataSend_isHex();
                DataDeal humidityQuery = new DataDeal(sp, "0x5502aa");
                humidityQuery.DataSend_isHex();
                isQuery = true;
                //System.Threading.Thread.Sleep(200);  //延时200ms等待数据的发送；
                for (long i = 0; i < 10000000; i++)                   //做个延时，等待数据接收完成；
                {
                    if (canRead)
                        break;
                }
                if (!canRead)
                {
                    MessageBox.Show("Failed To Query!");
                    return;
                }
                else
                {
                    string humidity;
                    humidity = recvDataStore;
                    if (humidity.Substring(2, 2) == "55" && humidity.Substring(humidity.Length - 2) == "aa")      //判断是否是合法接收字符串
                    {
                        humidity = humidity.Replace("0x", "");
                        humidity = humidity.Replace("0X", "");
                        humidity.Replace("aa", "");
                        int humidityNum = Int32.Parse(humidity, System.Globalization.NumberStyles.HexNumber);
                        humidity = humidityNum.ToString();
                        TemperatureOutput.Value = humidity;
                        TemperatureResult.Text = humidity;
                    }
                    else
                    {
                        MessageBox.Show("Illegal Temperature Message", messageBoxTitle, messageBoxButton, messageBoxImage);
                        isQuery = false;
                        canRead = false;
                        return;
                    }
                    isQuery = false;
                    canRead = false;

                }
            }
            catch (Exception)
            {
                MessageBox.Show("ERRO!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
            }
        }
        #endregion
        #region FUNCTION QUERY TEMPERATURE
        private void TemperatureQueryButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                //sendData = "0x5501aa";
                // dataSend_isHex();
                DataDeal temperQuery = new DataDeal(sp, "0x5501aa");
                temperQuery.DataSend_isHex();
                isQuery = true;
                //System.Threading.Thread.Sleep(200);  //延时200ms等待数据的发送；
                for (long i = 0; i < 10000000; i++)                   //做个延时，等待数据接收完成；
                {
                    if (canRead)
                        break;
                }
                if (!canRead)
                {
                    MessageBox.Show("Failed To Query!");
                    return;
                }
                else
                {
                    string temperature;
                    temperature = recvDataStore;
                    if (temperature.Substring(2, 2) == "55" && temperature.Substring(temperature.Length - 2) == "aa")      //判断是否是合法接收字符串
                    {
                        temperature = temperature.Replace("0x", "");
                        temperature = temperature.Replace("0X", "");
                        temperature.Replace("aa", "");
                        int temperNum = Int32.Parse(temperature, System.Globalization.NumberStyles.HexNumber);
                        temperature = temperNum.ToString();
                        TemperatureOutput.Value = temperature;
                        TemperatureResult.Text = temperature;
                    }
                    else
                    {
                        MessageBox.Show("Illegal Temperature Message", messageBoxTitle, messageBoxButton, messageBoxImage);
                        isQuery = false;
                        canRead = false;
                        return;
                    }
                    isQuery = false;
                    canRead = false;

                }
            }
            catch (Exception)
            {
                MessageBox.Show("ERRO!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
            }
        }
        #endregion
        # region FUNCTION SET SPEED 
        private void SpeedSettingButton_Click(object sender, RoutedEventArgs e)
        {
            if (SpeedSettingBox.Text != "")
            {
                try
                {
                    string speedSendValue = null;
                    speedSendValue = "0X5503";
                    int speedNum = Convert.ToInt32(SpeedSettingBox.Text);
                    speedSendValue += speedNum.ToString("X4");
                    speedSendValue += "AA";
                    //sendData = speedSendValue;
                    //dataSend_isHex();
                    DataDeal speedSet = new DataDeal(sp, speedSendValue);
                    speedSet.DataSend_isHex();
                }
                catch (Exception)
                {
                    MessageBox.Show("Invalid Speed Value\n", messageBoxTitle, messageBoxButton, messageBoxImageWarning);
                }
            }
            else
            {
                MessageBox.Show("Please Input The Speed Value!\n");
            }
        }
        #endregion
        #region FUNCTION CHECK HEX SEND 
        private void HexSendCheckBox_Checked(object sender, RoutedEventArgs e)
        {
            isSendHex = true;
        }
        private void HexSendCheckBox_UnChecked(object sender, RoutedEventArgs e)
        {
            isSendHex = false;
        }
        #endregion
        #region FUNCTION CHECK HEX RECIEVE
        private void HexRecieveCheckBox_Checked(object sender, RoutedEventArgs e)
        {
            isRecieveHex = true;
        }
        private void HexRecieveCheckBox_UnChecked(object sender, RoutedEventArgs e)
        {
            isRecieveHex = false;
        }
        #endregion
        #region FUNCTION DATA RECIEVE 
        private void sp_DataReceived(object sender, System.IO.Ports.SerialDataReceivedEventArgs e)
        {
            System.Threading.Thread.Sleep(100);//延时 100ms 等待接收完数据
                                               //this.Invoke 就是跨线程访问 ui 的方法，也是本文的范例
            this.Dispatcher.Invoke((delegate
            { 
                try
                {
                    if (isRecieveHex == false)
                    {
                        // recvDataStore = sp.ReadLine();
                        DataRecieveBox.Text += sp.ReadExisting();
                      //  DataRecieveBox.Text += recvDataStore;
                        if (isQuery)
                            canRead = true;
                        //sp.DiscardInBuffer();
                    }
                    else
                    {
                        Byte[] ReceivedData = new Byte[sp.BytesToRead]; //创建接收字节数组
                        sp.Read(ReceivedData, 0, ReceivedData.Length); //读取所接收到的数据
                        String RecvDataText = null;
                        RecvDataText += "0X";
                        for (int i = 0; i < ReceivedData.Length - 1; i++)
                        {
                            RecvDataText += ReceivedData[i].ToString("X2");
                        }
                        recvDataStore = RecvDataText;
                        if (isQuery)
                            canRead = true;
                        RecvDataText += " ";
                        DataRecieveBox.Text += RecvDataText;
                    }
                    sp.DiscardInBuffer();//丢弃接收缓冲区数据，每次读写之后全部都需要丢弃缓冲区
                }
                catch (Exception)
                {
                    MessageBox.Show("Erro Accur!");
                }
            }));
        }
        #endregion
   /*     #region FUNCTION SEND DATA
        public void dataSend_notHex()
        {
            try
            {
                if ((isOpen) && ( sendData!= ""))                   //Send Data;
                {
                    try
                    {
                       sp.DiscardOutBuffer();
                       sp.WriteLine(sendData);
                        MessageBox.Show("Successfully Send!\n", messageBoxTitle, messageBoxButton);
                 //       System.Threading.Thread.Sleep(10);
                        //DataRecieveBox.Text= sp.ReadLine();
                    }
                    catch (Exception)
                    {
                        MessageBox.Show("Send Failed!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
                        return;
                    }
                }
                else if (sendData == "")                                       //Data is empty;
                {
                    MessageBox.Show("Illegal Sending Data. Input Again!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
                }
                else
                {
                    MessageBox.Show("Port Is Not Opened Normally!\n", messageBoxTitle, messageBoxButton, messageBoxImageWarning);
                }
            }
            catch (Exception)
            {
                MessageBox.Show("Send Failed!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
            }
        }
        public void dataSend_isHex()
        {
            try
            {
                if (sp.IsOpen && (sendData != "") && (sp != null))
                {
                    // sp.DiscardOutBuffer();
                    sendData = sendData.Replace("0x", "");                     //去掉0x,0X字符串;
                    sendData = sendData.Replace("0X", "");
                    sendData = sendData.Replace(" ", "");                         //去掉空格；
                    byte[] sendBytes = new byte[sendData.Length / 2];
                    int j = 0;
                    for (int i = 0; i < sendData.Length; i += 2)
                    {
                        sendBytes[j++] = Convert.ToByte(Int32.Parse(sendData.Substring(i, 2), System.Globalization.NumberStyles.HexNumber));  //将输入的十六进制String转化为Byte数组；
                    }
                    sp.Write(sendBytes, 0, sendData.Length / 2);
                    MessageBox.Show("Send Successfully!\n", messageBoxTitle, messageBoxButton);
                }
                else if (sp.IsOpen == false)
                {
                    MessageBox.Show("Please Open The Port!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
                }
                else
                {
                    MessageBox.Show("Please Input The Sending Data!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
                }
            }
            catch (Exception)
            {
                MessageBox.Show("Unable To Send Data!\n", messageBoxTitle, messageBoxButton, messageBoxImage);
            }
        }
#endregion*/
    }


    #endregion
}
