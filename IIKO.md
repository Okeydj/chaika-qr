## Настройка Qr код чайка на пречеке в IIKO
---
#### 1. Для начала необходимо настроить шаблон пречека.

Данную функцию необходимо вставить в шаблон пречека, в зависимости от нужного функционала.

В переменной   `var defaultWaiterId = "1000";` надо прописать id официанта по умолчанию, если вдруг не получится определить номер вставит стандартный кошелёк

#### Рассчет чаевых от суммы чека.
Если необходимо ,чтобы форма оплаты отображала проценты от суммы чека:

<img src="https://raw.github.com/Okeydj/chaika-qr/master/percent_qr_tips.png" width="500px" height="400px">



  ```c#
  @helper ChaikaQrCode()
{
//Функция формирования qr и текста чаевых чайка с суммой 
    var lkUrl = "https://pay.gochaika.ru/tips/";
    var orderSummUrl = "?order_summ=";
    //код официанта по умолчанию, если не найден в имени
    var defaultWaiterId = "1000"; 
    var waiterId = defaultWaiterId;
    var order = Model.Order;
    var fullSum = order.GetFullSum() - order.DiscountItems.Where(di => !di.Type.PrintProductItemInPrecheque).Sum(di => di.GetDiscountSum());
    var categorizedDiscountItems = new List<IDiscountItem>();
    var nonCategorizedDiscountItems = new List<IDiscountItem>();
    var subTotal = fullSum - categorizedDiscountItems.Sum(di => di.GetDiscountSum());
    var totalWithoutDiscounts = subTotal - nonCategorizedDiscountItems.Sum(di => di.GetDiscountSum());
    var prepay = order.PrePayments.Sum(prepayItem => prepayItem.Sum);
    var total = Math.Max(totalWithoutDiscounts + order.GetVatSumExcludedFromPrice() - prepay, 0m);
    if (Model.Order.Waiter.Name != null)
    {
        string[] wData = (Model.Order.Waiter.Name).Split('#');
        if(wData.Length >= 2)
        {
            waiterId = wData[wData.Length - 1];
        }
        else
        {
            waiterId = defaultWaiterId;
        }
    }
    else
    {
        waiterId = defaultWaiterId;
    }
    <center><split><whitespace-preserve><f2>@("Чаевые картой")</f2></whitespace-preserve></split></center>
    <center><split><whitespace-preserve>Наведите камеру на QR-код:</whitespace-preserve></split></center>
    <center><qrcode size="small" correction="medium">@(lkUrl + waiterId + orderSummUrl + total)</qrcode></center>
    <center><split><whitespace-preserve>или зайдите на </whitespace-preserve></split></center>
    <center><split><whitespace-preserve>@("https://chaika.tips/" + waiterId)</whitespace-preserve></split></center>
      <f1><center>Чайка</center></f1>        
    <center><split><whitespace-preserve>прием чаевых банковской картой.</whitespace-preserve></split></center>
}
  ```

#### Статическая сумма чаевых.
Если необходимо, чтобы форма оплаты отображала кнопки со статическими суммами: 

<img src="https://raw.github.com/Okeydj/chaika-qr/master/static_qr_tips.png" width="500px" height="400px">


  ```c#
  @helper ChaikaQrCode()
{
//Функция формирования qr и текста чаевых чайка с суммой 
    var lkUrl = "https://pay.gochaika.ru/tips/";
    //код официанта по умолчанию, если не найден в имени
    var defaultWaiterId = "1000"; 
    var waiterId = defaultWaiterId;

    if (Model.Order.Waiter.Name != null)
    {
        string[] wData = (Model.Order.Waiter.Name).Split('#');
        if(wData.Length >= 2)
        {
            waiterId = wData[wData.Length - 1];
        }
        else
        {
            waiterId = defaultWaiterId;
        }
    }
    else
    {
        waiterId = defaultWaiterId;
    }
    <center><split><whitespace-preserve><f2>@("Чаевые картой")</f2></whitespace-preserve></split></center>
    <center><split><whitespace-preserve>Наведите камеру на QR-код:</whitespace-preserve></split></center>
    <center><qrcode size="small" correction="medium">@(lkUrl + waiterId)</qrcode></center>
    <center><split><whitespace-preserve>или зайдите на </whitespace-preserve></split></center>
    <center><split><whitespace-preserve>@("https://chaika.tips/" + waiterId)</whitespace-preserve></split></center>
      <f1><center>Чайка</center></f1>        
    <center><split><whitespace-preserve>прием чаевых банковской картой.</whitespace-preserve></split></center>
}
  ``` 
  
Необходимо выбрать один из этих вариантов и вставить под тегами </doc> в вашем шаблоне пречека

<img src="https://raw.github.com/Okeydj/chaika-qr/master/iiko_doc.png">

В теги doc в footer вставить вызов функции @ChaikaQrCode() 

<img src="https://raw.github.com/Okeydj/chaika-qr/master/iiko_doc_func.png">


#### 2.Настроить id официанта в карточках сотрудника 

Для того что бы в qr подставлялся нужный id официанта, к имени официанта в карточке сотрудника надо добавить #айдивчайке, например Александр Петров #1201 

<img src="https://raw.github.com/Okeydj/chaika-qr/master/emplo.png" >


Если не добавлять к имени, будет использовано id по умолчанию.

В итоге имя официанто должно выглядить так 

<img src="https://raw.github.com/Okeydj/chaika-qr/master/waiters.png" >


Сохраняем все изменения
