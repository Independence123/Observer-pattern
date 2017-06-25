# Observer-pattern
homework4

观察者模式
1.Subject类（主题或抽象通知者）
public abstract class Subject
{
    private IList<Observer> observers = new List<Observer>();
 
    /// <summary>
    /// 添加观察者
    /// </summary>
    /// <param name="observer">观察者</param>
    public void Attach(Observer observer)
    {
        observers.Add(observer);
    }
 
    /// <summary>
    /// 移除观察者
    /// </summary>
    /// <param name="observer">观察者</param>
    public void Detach(Observer observer)
    {
        observers.Remove(observer);
    }
 
    /// <summary>
    /// 通知观察者
    /// </summary>
    public void Notify()
    {
        foreach (Observer o in observers)
        {
            o.Update();
        }
    }
}

2.ConcreteSubject类，具体主题或者具体通知者
public class ConcreteSubject:Subject
{
    private string _subjectState;
 
    /// <summary>
    /// 具体被观察者状态
    /// </summary>
    public string SubjectState
    {
        get { return _subjectState; }
        set { _subjectState = value; }
    }
}

3.Observer抽象观察者，为所有的具体观察者定义一个接口
public abstract class Observer
{
    public abstract void Update();
}

4.ConcreteObserver具体观察者
/// <summary>
/// 具体观察者，实现抽象观察者角色所要求的更新接口
/// 以便使本身的状态与主题的状态相协调
/// </summary>
public class ConcreteObserver:Observer
{
    private string name;
    private string observerState;
    private ConcreteSubject subject;
 
    public ConcreteSubject Subject
    {
        get { return subject; }
        set { subject = value; }
    }
 
    public ConcreteObserver(ConcreteSubject subject,string name)
    {
        this.subject = subject;
        this.name = name;
    }
 
    public override void Update()
    {
        observerState = subject.SubjectState;
        Console.WriteLine("观察者{0}的新状态是{1}",name,observerState);
    }
}


5.客户端代码
static void Main(string[] args)
{
    ConcreteSubject cs = new ConcreteSubject();
    cs.Attach(new ConcreteObserver(cs,"James"));
    cs.Attach(new ConcreteObserver(cs,"Jane"));
 
    cs.SubjectState="OK";
    cs.Notify();
    Console.Read();
}

观察者模式示例
1.Stock股票类
public class Stock
{
    private double _openPrice;
    private double _closePrice;
    public delegate void PriceChangedHandler(object sender, StockDetailArgs e);
    public event PriceChangedHandler PriceChanged;
 
    public double OpenPrice
    {
        get { return _openPrice; }
        set { _openPrice = value; }
    }
    public double ClosePrice
    {
        get { return _closePrice; }
        set { _closePrice = value; }
    }
 
    public void StartTrading()
    {
        double current;
 
        //Current price decrements by $1.00 as the stock is traded  
        current = OpenPrice;
 
        while (current > ClosePrice)
        {
            //Stock is falling in increments of $1.00  
            current = current - 1.00;
 
            //Call the method to raise the event  
            OnPriceChanged(current);
 
            //Simulate a delay of 2000ms between market price updates  
            System.Threading.Thread.Sleep(2000);
        }
    }
 
    protected void OnPriceChanged(double currentMarketPrice)
    {
        //Any handlers attached to this event?
        if (PriceChanged != null)
        {
            StockDetailArgs args = new StockDetailArgs();
            args.CurrentPrice = currentMarketPrice;
            Console.WriteLine("当前股票价格是：" + args.CurrentPrice.ToString());
            ////Raise the event
            PriceChanged(this, args);
        }
    }
 
    /// <summary>
    /// 添加观察者
    /// </summary>
    /// <param name="observer">观察者</param>
    public void AttachEvent(IObserver observer)
    {
        PriceChanged += new PriceChangedHandler(observer.Stoc_PriceChanged);
    }
}

