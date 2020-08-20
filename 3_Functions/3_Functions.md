![alt text](img_3.1.png)
<div dir='rtl'>

# توابع
در اوایل برنامه نویسی ، سیستم های خود را باroutine ها و subroutine ها ساختیم. سپس، در عصر Fortran و PL / 1 سیستم هایمان را با برنامه ها، زیر برنامه ها و توابع ساختیم. امروزه تنها توابع هستند که از آن دوران اولیه باقی مانده اند. توابع اولین خط سازماندهی در هر برنامه هستند. خوب نوشتن آنها موضوع این فصل است.
کد ارائه شده در listing 3-1 را در نظر بگیرید. پیدا کردن یک تابع طولانی در FitNesse1 دشوار است ، اما پس از کمی جستجو به این مورد رسیدم. این تابع نه تنها طولانی است ، بلکه کد تکراری، تعداد زیادی رشته عجیب و غریب و انواع مختلف داده ها و API های عجیب و مبهم را نیز داراست. ببینید که در مدت سه دقیقه آینده چقدر از آن را می توانید درک کنید.
</div>

Listing 3-1
HtmlUtil.java (FitNesse 20070619)


```java
public static String testableHtml(PageData pageData,boolean includeSuiteSetup) throws Exception {
 WikiPage wikiPage=pageData.getWikiPage();
 StringBuffer buffer=new StringBuffer();
 if(pageData.hasAttribute("Test")){
	 if(includeSuiteSetup){
		 WikiPage suiteSetup=PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_SETUP_NAME,wikiPage);
		 if(suiteSetup!=null){
			 WikiPagePath pagePath=suiteSetup.getPageCrawler().getFullPath(suiteSetup);
			 String pagePathName=PathParser.render(pagePath);
			 buffer.append("!include -setup .")
				 .append(pagePathName)
				 .append("\n");
		 }
	 }
	 WikiPage setup=PageCrawlerImpl.getInheritedPage("SetUp",wikiPage);
	 if(setup!=null){
		 WikiPagePath setupPath=wikiPage.getPageCrawler().getFullPath(setup);
		 String setupPathName=PathParser.render(setupPath);
		 buffer.append("!include -setup .")
			 .append(setupPathName)
			 .append("\n");
	 }
 }

 buffer.append(pageData.getContent());
 if(pageData.hasAttribute("Test")){
	 WikiPage teardown=PageCrawlerImpl.getInheritedPage("TearDown",wikiPage);
	 if(teardown!=null){
		 WikiPagePath tearDownPath=wikiPage.getPageCrawler().getFullPath(teardown);
		 String tearDownPathName=PathParser.render(tearDownPath);
		 buffer.append("\n")
			 .append("!include -teardown .")
			 .append(tearDownPathName)
			 .append("\n");
	 }

	 if(includeSuiteSetup){
		 WikiPage suiteTeardown=PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_TEARDOWN_NAME,wikiPage);
		 if(suiteTeardown!=null){
			 WikiPagePath pagePath=suiteTeardown.getPageCrawler().getFullPath(suiteTeardown);
			 String pagePathName=PathParser.render(pagePath);
			 buffer.append("!include -teardown .")
			 .append(pagePathName)
			 .append("\n");
		 }
	 }
 }
 pageData.setContent(buffer.toString());
 return pageData.getHtml();
}
```
<div dir='rtl'>
آیا بعد از سه دقیقه مطالعه تابع آن را درک می کنید؟ احتمالا نه. اتفاقات زیادی در سطوح تجرید بسیار متفاوت رخ میدهد. رشته ها و فراخوانی های توابع عجیب و غریبی وجود دارد که با زوج جمله های شرطی تو در تو که توسط flag ها کنترل میشوند ترکیب شده اند.
با این حال ، تنها با چند استخراج متد ساده ، تغییر نام و کمی تغییر ساختار ، توانستم هدف این تابع را در 9 سطر در listing 3-2 درج کنم. ببینید که آیا می توانید در 3 دقیقه آینده آن را درک کنید.

</div>

Listing 3-2
HtmlUtil.java (refactored)

```java
    public static String renderPageWithSetupsAndTeardowns( PageData, boolean isSuite) throws Exception 
    {
    boolean isTestPage = pageData.hasAttribute("Test");
        if (isTestPage) 
	{
            WikiPage testPage = pageData.getWikiPage();
            StringBuffer newPageContent = new StringBuffer();
            includeSetupPages(testPage, newPageContent, isSuite);
            newPageContent.append(pageData.getContent());
            includeTeardownPages(testPage, newPageContent, isSuite);
            pageData.setContent(newPageContent.toString());
        }
    return pageData.getHtml();
    }
```
<div dir='rtl'>
مگر اینکه دانشجوی FitNesse باشید که همه جزئیات را بفهمید. با این وجود ، شما احتمالاً می فهمید كه این تابع شامل وارد كردن برخی از صفحات setup و teardown به یك صفحه تست و تبدیل آن صفحه به HTML است. اگر باJUnit  آشنا باشید ، احتمالاً می دانید که این تابع متعلق به نوعی چارچوب تست مبتنی بر وب است. و البته که درست است. استنباط اطلاعات از listing 3-2 بسیار آسان است ، اما با listing 3-1 اطلاعات کاملاً مبهم است.
بنابراین چه چیزی خواندن و درک یک تابع مانند Listing 3-2 را آسان می کند؟ چگونه می توانیم یک تابع را با هدف آن مرتبط کنیم؟ چه ویژگی هایی را می توانیم به توابع خود بدهیم تا امکان درک نوع برنامه ای که آن تابع در آن قرار دارد را به یک خواننده تصادفی 
بدهد؟

### کوچک بودن!
اولین قانون برای توابع این است که آنها باید کوچک باشند. قانون دوم این است که آنها باید از آن هم کوچکتر باشند. این ادعایی نیست که بتوانم توجیه کنم. من نمی توانم به تحقیقاتی اشاره کنم که نشان می دهد توابع بسیار کوچک بهتر هستند. چیزی که می توانم به شما بگویم این است که من نزدیک به چهار دهه توابع مختلفی در ابعاد مختلف نوشتم. چندین مورد ناپسند 3000 خطی نوشتم. من تعداد زیادی تابع در حدود 100 تا 300 خط نوشتم. و توابعی به طول 20 تا 30 خط نوشته ام. آنچه این تجربه از طریق آزمایش طولانی و خطا به من آموخته است اینست که توابع باید بسیار کوچک باشند.
در دهه هشتاد می گفتیم که یک تابع نباید بزرگتر از یک صفحه نمایش باشد. البته این را در زمانی گفتیم که صفحه های VT100, 24 خط در 80 ستون بودند و ویرایشگران ما تنها از 4 خط برای مقاصد مدیریتی استفاده می کردند. امروزه با یک فونت کوتاه شده و یک مانیتور بزرگ خوب ، می توانید 150 کاراکتر را روی یک خط و 100 خط یا بیشتر را در یک صفحه قرار دهید. خطوط نباید 150 کاراکتر داشته باشد. توابع نباید 100 خط باشند. توابع باید به سختی به طول 20 خط برسند.
یک تابع چقدر باید کوتاه باشد؟ در سال 1999 من برای دیدن Kent Beck به خانه اش در Oregon رفتم. ما نشستیم و با هم برنامه نویسی کردیم. در یک لحظه او یک برنامه Java/Swing کوچک زیبا را به من نشان داد که آن را Sparkle نامید. این اثر یک جلوه بصری بسیار شبیه به چوب جادویی الهه افسانه ای در فیلم سیندرلا ایجاد کرد. با حرکت دادن ماوس ، جرقه ها با یک قلاب رضایت بخش از مکان نما بیرون می ریزند و از طریق یک میدان گرانشی شبیه سازی شده به پایین پنجره می افتادند. وقتی Kent کد را به من نشان داد ، من از این که همه توابع چقدر کوچک هستند ، تحت تأثیر قرار گرفتم. من به توابع برنامه های Swing که فضای عمودی زیادی اشغال میکنند، عادت داشتم. هر تابع در این برنامه فقط دو ، سه یا چهار خط طول داشت. هر کدام واضح و آشکار بود. هر کدام داستانی را بیان می کرد. و هر کدام شما را به ترتیبی قانع کننده به سمت بعدی سوق می دادند. این مقدار همانی مقداری است که توابع شما باید به آن اندازه کوچک باشند!1
توابع شما چقدر کوتاه باشد؟ آنها معمولاً باید از listing 3-2 کوتاه تر باشند! در واقع ، listing 3-2 باید به اندازه listing 3-3 کوتاه شود.( من از Kent سؤال کردم که آیا او هنوز یک نسخه دارد ، اما او نتوانست یکی از آنها را پیدا کند. من تمام رایانه های قدیمی خود را نیز جستجو کردم ، اما فایده ای نداشت. تمام آنچه اکنون باقی مانده است ، خاطره من از آن برنامه است.)
</div>

Listing 3-3
HtmlUtil.java (re-refactored)

```java
public static String renderPageWithSetupsAndTeardowns(PageData pageData, boolean isSuite) throws Exception 
{
    if (isTestPage(pageData))
        includeSetupAndTeardownPages(pageData, isSuite);
	
    return pageData.getHtml();
}
```
<div dir='rtl'>

## بلوک ها و تو رفتگی ها
این listing نشان میدهد که بلوک های موجود در جملات شرطی ،  حلقه ها ، و نظایر آن، باید به اندازه یک خط باشند. احتمالاً آن خط باید یک فراخوانی تابع باشد. این کار نه تنها تابع محصور را کوچک نگه می دارد بلکه یک ارزش مستندسازی را نیز به آن اضافه می کند زیرا تابع فراخوانی شده در آن بلوک می تواند نام توصیفی خوبی داشته باشد.
همچنین این listing نشان میدهد توابع نباید برای نگه داشتن ساختارهای تو در تو به اندازه کافی بزرگ باشند. بنابراین ، سطح تورفتگی یک تابع نباید بیشتر از یک یا دو باشد. این امر البته باعث می شود خواندن و درک توابع آسان تر شود.
</div>
<div dir='rtl'>

### انجام دادن یک کار دیگر


باید خیلی واضح باشد که لیست 3-1 بیش از یک کار انجام می دهد. این از جمله ایجاد بافر، واکشی صفحات، جستجوی صفحات وراثت، تفسیر مسیرها، افزودن رشته های arcane و ایجاد HTML از جمله موارد دیگر است. لیست 3-1 بسیار مشغول انجام کارهای مختلف است. از طرف دیگر ، لیست 3-3 یک کار ساده را انجام می دهد. این شامل setups و teardowns به صفحات تست است.

توصیه های زیر به مدت 30 سال یا بیشتر به یک شکل یا شکل دیگر ظاهر شده است.

**توابع باید یک کار را انجام دهند. باید خوب انجامش دهند. آنها فقط باید این کار را انجام دهند.**

مشکلی که در این گفته وجود دارد این است که دانستن اینکه "یک کار" چیست دشوار است. آیا لیست 3-3 یک کار را انجام می دهد؟ به راحتی می توان موزدی را درنظر گرفت که سه کار را انجام می دهد:

1. تعیین اینکه آیا این صفحه یک صفحه آزمایشی است یا خیر.
2. در این صورت ، تنظیمات و درهم دریدن  شامل شود.
3. صفحه در HTML پردازش شود.

پس کدام است؟ آیا تابع یک کار را انجام می دهد یا سه کار؟ توجه کنید که سه مرحله از عملکرد، تنها یک سطح انتزاع زیر نام بیان شده تابع است. میتوانیم تابع را به عنوان یک بند کوتاه TO شرح دهیم:

TO RenderPageWithSetupsAndTeardowns، چک میکنیم که صفحه آیا صفحه تست است و اگر بود، setups و teardowns را اضافه می‌کنیم. در هر صورت، صفحه را به HDML رندر میکنیم.

اگر یک تابع فقط آن مراحل را انجام دهد که یک سطح زیر نام بیان شده تابع باشد، آنگاه تابع یک کار را انجام می دهد. از این گذشته ، دلیل اینکه ما توابع را می نویسیم ، تجزیه مفهوم بزرگتر (به عبارت دیگر ، نام تابع) در مجموعه مراحل در سطح بعدی انتزاع است.

باید خیلی واضح باشد که لیست 3-1 شامل مراحل در بسیاری از سطوح مختلف انتزاع است. بنابراین به وضوح بیش از یک کار را انجام می دهد. حتی لیست 3-2 دارای دو سطح انتزاع است ، که با توانایی ما در شکاندن آن اثبات می شود. اما شکاندن لیست 3-3 بسیار سخت خواهد بود.میتوانیم قسمت if را به صورت متدی بنام includeSetupsAndTeardownsIfTestPage استخراج کنیم، اما باز کد را بدون تغییر سطح انتزاع بیان می‌کند.

بنابراین ، راه دیگری برای دانستن اینکه یک تابع بیشتر از "یک کار" انجام می دهد ، این است که اگر شما می توانید تابع دیگری را از آن با نامی استخراج کنید که صرفاً بازگویی در اجرای آن نیست [G34].

### بخش‌های داخت توابع

به Listing 4-7 در صفحه ۷۱ نگاه کنید. درنظر داشته باشید که تابع generatePrimes به بخش‌هایی مثل declarations، initializations و sieve تقسیم میشود. این یک نشانه بارز انجام بیش از یک کار است. توابعی که یک کار را انجام می دهند به طور منطقی نمی توانند به بخشها تقسیم شوند.

## یک سطح انتزاع به ازای هر تابع

برای اینکه مطمئن شویم توابع ما "یک کار" را انجام می دهند ، باید اطمینان حاصل کنیم که statements موجود در تابع، در یک سطح انتزاع قرار دارند. به راحتی می توان دید که چگونه Listing 3-1 این قانون را نقض می کند. مفاهیمی در آنجا وجود دارند که در سطح بسیار بالایی از انتزاع قرار دارند، مثل getHtml()؛ مابقی در سطح متوسطی از انتزاع هستند، مثل: String pagePathName = PathParser.render(pagePath)؛ و مابقی هنوز بصورت قابل ملاحظه‌ای سطح پایین هستند، مثل: .append("\n") .

ترکیب سطوح مختلف انتزاع در یک تابع همیشه گیج‌کننده است. خوانندگان ممکن است نتوانند بگویند آیا یک عبارت خاص یک مفهوم اساسی است یا یک جز. بدتر ، مانند پنجره های شکسته ، هنگامی که جزئیات با مفاهیم اساسی مخلوط می شوند ، جزئیات بیشتر و بیشتری تمایل به پیوستن به درون تابع دارند.

## خواندن کد از بالا به پایین: قاعده گام به گام

می خواهیم کد مانند روایتی از بالا به پایین خوانده شود. می خواهیم هر تابع با سطح بعدی انتزاع دنبال شود تا بتوانیم برنامه را بخوانیم ، در حالی که لیست توابع را می خوانیم ، یک سطح انتزاع کم می شود. من این را قاعده گام به گام می نامم.

برای طور دیگر گفتن این حرف، می خواهیم بتوانیم برنامه را بخوانیم گویی مجموعه ای از پاراگراف های TO است, هرکدام از آنها سطح فعلی انتزاع را توصیف کرده و  به پاراگرافهای TO پسین در سطح پایین بعدی اشاره می کند.

*TO include setups و teardowns، include setups، سپس include محتوای صفحه تست، و سپس include teardowns.*

*TO include setups، include suite setup اگر یک suite وجود دارد، سپس include setup معمولی*

*TO include suite setup، دنبال والد سلسله مراتبی برای صفحه “SuiteSetUp” میگردیم و یک include statement با مسیر صفحه اضافه می کنیم.*

*TO دنبال والد بگرد...*

به نظر می رسد که برای برنامه نویسان خیلی سخت است که یاد بگیرند از این قاعده پیروی کنند و توابعی را بنویسند که در یک سطح انتزاعی باقی بمانند. اما یادگیری این ترفند نیز بسیار مهم است. این، کلید کوتاه نگه داشتن توابع و اطمینان از انجام "یک کار" آنها است. نوشتن كد بصورتیکه مانند مجموعه بالا به پایین پاراگرافهای TO خوانده شود، یك روش مؤثر برای ثابت نگه داشتن سطح انتزاع است.

در پایان این فصل به Listing 3-7 نگاهی بیندازید. کل تابع testableHtml را نشان میدهد که مطابق با اصول شرح داده شده در اینجا بازنویسی شده‌است. توجه کنید که چگونه هر تابع، تابع بعدی را معرفی می کند ، و هر تابع در یک سطح ثابت از انتزاع باقی می ماند.

### Switch Statements

خیلی سخت است که *switch* statement کوچکی ساخت. حتی یک *switch* statement فقط با دو مورد بزرگتر از آن است که دوست داشته باشم یک بلوک یا یک عملکرد باشد. حتی ساختن یک *switch* statement که یک کار انجام دهد، سخت است. براساس ماهیت آنها، *switch* statement همیشه N مورد را انجام می دهند. متأسفانه ، ما همیشه نمی توانیم از *switch* statements پرهیز کنیم ، اما می توانیم اطمینان حاصل کنیم که هر *switch* statement در یک کلاس سطح پایین قرار دارد و هرگز تکرار نمی شود. این کار را با چند ریختی (polymorphism) انجام می دهیم.

Listing 3-4 را در نظر بگیرید. تنها یکی از عملیاتی را نشان می دهد که ممکن است به نوع کارمند وابسته باشد.

</div>

Listing 3-4
Payroll.java

```java
public Money calculatePay(Employee e) throws InvalidEmployeeType 
{
    switch (e.type) 
    {
	case COMMISSIONED:
	    return calculateCommissionedPay(e);
	case HOURLY:
	    return calculateHourlyPay(e);
	case SALARIED:
	    return calculateSalariedPay(e);
	default:
	    throw new InvalidEmployeeType(e.type);
    }
}
```

<div dir='rtl'>
در این تابع چندین مشکل وجود دارد. اولین مشکل بزرگ بودن آن است و زمانی که انواع کارمندان جدید اضافه شد، رشد خواهد کرد. دومین مشکل خیلی واضح است که بیش از یک چیز را انجام می دهد. سومین مشکل این است که اصل مسئولیت واحد 7 (SRP) را نقض می کند زیرا بیش از یک دلیل برای تغییر آن وجود دارد. چهارمین مشکل این است که اصل Open Closed Principle 8 را نقض می کند زیرا هر وقت نوع جدیدی اضافه شود باید تغییر کند.
اما شاید بدترین مشکل این تابع وجود تعداد نامحدودی از توابع دیگر است که همین ساختار را خواهند داشت. مثلاً می توانستیم داشته باشیم
isPayday(Employee e, Date date),
یا
deliverPay(Employee e, Money pay),
یا غیره
همه اینها ساختار نابسامان یکسانی دارند. راه حل این مشکل (رجوع کنید به لیست3-5) این است که عبارت switch را در یک ABSTRACT FACTORY قرار دهید و هرگز اجازه ندهید کسی آن را ببیند.
این کارخانه برای ایجاد نمونه های مناسب از مشتقات Employee از عبارت switch استفاده خواهد کرد و توابع مختلف مثل calculatePay ، isPayday و deliverPay 
از طریق واسط کارمند (Employee interface) به صورت چند ریختی ارسال خواهد شد. قانون کلی من برای عبارات switch این است که اگر فقط یک بار ظاهر شوند و برای ایجاد اشیا چند ریختی استفاده شوند و در پشت یک ارث بری مخفی شوند قابل تحمل خواهند بود.
</div>

Listing 3-5 Employee and Factory

```java
public abstract class Employee 
{
    public abstract boolean isPayday();
    public abstract Money calculatePay();
    public abstract void deliverPay(Money pay);
}
```
-----------------
```java
public interface EmployeeFactory 
{
     public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}
```
-----------------
```java
public class EmployeeFactoryImpl implements EmployeeFactory 
{
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType 
    {
        switch (r.type) 
	{
	    case COMMISSIONED:
		return new CommissionedEmployee(r) ;
	    case HOURLY:
		return new HourlyEmployee(r);
	    case SALARIED:
		return new SalariedEmploye(r);
	    default:
		throw new InvalidEmployeeType(r.type);
	}
   }
}
```

<div dir='rtl'>

روابطی که بقیه ی سیستم نمی تواند ببینتشان [G23]. البته هر شرایطی یکتاست و مواقعی وجود دارد که من یک یا چند بخش از آن قانون را نقض می کنم.

### استفاده از نام های توصیفی

در Listing 3-7 نام تابع مثالمان را از testableHtml به SetupTeardownIncluder.render تغییر دادم. این یک نام به مراتب بهتر است زیرا تابع را بهتر توصیف می کند. همچنین به هریک از توابع خصوصی نام توصیفی بهتری مانند isTestable یا SetupAndTeardownPages دادم. ارزش گذاری نام های خوب کار سختی است. اصل Ward را بیاد بیاور: "وقتی هر روال تقریباً همان چیزی است که انتظار داشتید، آن موقع دارید تمیز کد میزنید." نیمی از نبرد برای دستیابی به آن اصل ، انتخاب نام های خوب برای توابع کوچکی است که یک کار را انجام می دهند. هرچه تابعی کوچکتر و متمرکزتر باشد ، انتخاب نام توصیفی آسان تر است.

از ایجاد نام طولانی نترسید. یک نام توصیفی طولانی بهتر از یک اسم مبهم کوتاه است. یک نام توصیفی طولانی بهتر از یک توضیحات توصیفی طولانی است. از یک قرارداد نامگذاری استفاده کنید که اجازه می دهد چندین کلمه به راحتی در نام های تابع خوانده شود ، و سپس از آن کلمات چندگانه استفاده کنید تا یک نامی برای تابع انتحاب کنید که می گوید چه کاری انجام می دهد.

از صرف وقت خود برای انتخاب نام نترسید. در واقع ، شما باید چندین نام مختلف را امتحان کنید و کد را با هر یک از نام ها در محل بخوانید. IDE های مدرن مانند Eclipse یا IntelliJ تغییر نام ها را بسیار ساده می کند. از یکی از آن IDE ها استفاده کنید و با نام های مختلف تست کنید. تا زمانی انجام دهید که یکی از آنها، به همان توصیفی است که کد را نوشتید.

انتخاب نام های توصیفی ، طراحی ماژول را در ذهن شما روشن می کند و به شما در بهبود آن کمک می کند. به هیچ وجه غیر معمولی نیست که جستجوی نام خوب منجر به بازنویسی مطلوب کد شود.

در نامگذاری سکپارچه عمل کنید. از همان عبارات ، اسمها و افعال در نامهای تابع استفاده کنید که برای ماژول های خود انتخاب می کنید. بطور مثال، نام های *includeSetupAndTeardownPages*، *includeSetupPages*، *includeSuiteSetupPage* و *includeSetupPage*. اصطلاحات مشابه در این نامها به توالی اجازه می دهد داستانی را بازگو کنند. در واقع ، اگر من توالی بالا را به شما نشان دادم ، از خودتان می پرسید: "برای includeTeardownPages، includeSuiteTeardownPage و includeTeardownPage چه اتفاقی افتاد؟ " این چطوراست " . . تقریباً آنچه انتظار داشتید. "

## آرگومان های تابع



</div>

The ideal number of arguments for a function is zero (niladic). Next comes one (monadic), followed closely by two (dyadic). Three arguments (triadic) should be avoided where possible. More than three (polyadic) requires very special justification—and then shouldn’t be used anyway.

Arguments are hard. They take a lot of conceptual power. That’s why I got rid of almost all of them from the example. Consider, for instance, the StringBuffer in the example. We could have passed it around as an argument rather than making it an instance variable, but then our readers would have had to interpret it each time they saw it. When you are reading the story told by the module, includeSetupPage() is easier to understand than includeSetupPageInto(newPageContent) . The argument is at a different level of abstraction than the function name and forces you to know a detail (in other words, StringBuffer ) that isn’t particularly important at that point.

Arguments are even harder from a testing point of view. Imagine the difficulty of writing all the test cases to ensure that all the various combinations of arguments work properly. If there are no arguments, this is trivial. If there’s one argument, it’s not too hard.

With two arguments the problem gets a bit more challenging. With more than two arguments, testing every combination of appropriate values can be daunting. Output arguments are harder to understand than input arguments. When we read a function, we are used to the idea of information going in to the function through arguments and out through the return value. We don’t usually expect information to be going out through the arguments. So output arguments often cause us to do a double-take.

One input argument is the next best thing to no arguments. SetupTeardownIncluder.render(pageData) is pretty easy to understand. Clearly we are going to render the data in the pageData object.

## Common Monadic Forms
There are two very common reasons to pass a single argument into a function. You may be asking a question about that argument, as in boolean fileExists(“MyFile”) . Or you may be operating on that argument, transforming it into something else and returning it. For example, InputStream fileOpen(“MyFile”) transforms a file name String into an InputStream return value. These two uses are what readers expect when they see a function. You should choose names that make the distinction clear, and always use the two forms in a consistent context. (See Command Query Separation below.)

A somewhat less common, but still very useful form for a single argument function, is an event. In this form there is an input argument but no output argument. The overall program is meant to interpret the function call as an event and use the argument to alter the state of the system, for example, void passwordAttemptFailedNtimes(int attempts) . Use this form with care. It should be very clear to the reader that this is an event. Choose names and contexts carefully.

Try to avoid any monadic functions that don’t follow these forms, for example, void includeSetupPageInto(StringBuffer pageText) . Using an output argument instead of a return value for a transformation is confusing. If a function is going to transform its input argument, the transformation should appear as the return value. Indeed, StringBuffer transform(StringBuffer in) is better than void transform-(StringBuffer out) , even if the implementation in the first case simply returns the input argument. At least it still follows the form of a transformation.

## Flag Arguments
Flag arguments are ugly. Passing a boolean into a function is a truly terrible practice. It immediately complicates the signature of the method, loudly proclaiming that this function does more than one thing. It does one thing if the flag is true and another if the flag is false!
In Listing 3-7 we had no choice because the callers were already passing that flag in, and I wanted to limit the scope of refactoring to the function and below. Still, the method call render(true) is just plain confusing to a poor reader. Mousing over the call and seeing render(boolean isSuite) helps a little, but not that much. We should have split the function into two: renderForSuite() and renderForSingleTest() . 

## Dyadic Functions
A function with two arguments is harder to understand than a monadic function. For example, writeField(name) is easier to understand than writeField(output-Stream, name) . 10 Though the meaning of both is clear, the first glides past the eye, easily depositing its meaning. The second requires a short pause until we learn to ignore the first parameter. And that, of course, eventually results in problems because we should never ignore any part of code. The parts we ignore are where the bugs will hide.


There are times, of course, where two arguments are appropriate. For example, Point p = new Point(0,0); is perfectly reasonable. Cartesian points naturally take two arguments. Indeed, we’d be very surprised to see new Point(0) . However, the two arguments in this case are ordered components of a single value! Whereas output-Stream and name have neither a natural cohesion, nor a natural ordering.

Even obvious dyadic functions like assertEquals(expected, actual) are problematic. How many times have you put the actual where the expected should be? The two arguments have no natural ordering. The expected, actual ordering is a convention that requires practice to learn.

Dyads aren’t evil, and you will certainly have to write them. However, you should be aware that they come at a cost and should take advantage of what mechanims may be available to you to convert them into monads. For example, you might make the writeField method a member of outputStream so that you can say outputStream. writeField(name) . Or you might make the outputStream a member variable of the current class so that you don’t have to pass it. Or you might extract a new class like FieldWriter that takes the outputStream in its constructor and has a write method.

## Triads
Functions that take three arguments are significantly harder to understand than dyads. The issues of ordering, pausing, and ignoring are more than doubled. I suggest you think very carefully before creating a triad.

For example, consider the common overload of assertEquals that takes three arguments: assertEquals(message, expected, actual) . How many times have you read the message and thought it was the expected ? I have stumbled and paused over that particular triad many times. In fact, every time I see it, I do a double-take and then learn to ignore the message.
On the other hand, here is a triad that is not quite so insidious: assertEquals(1.0, amount, .001) . Although this still requires a double-take, it’s one that’s worth taking. It’s always good to be reminded that equality of floating point values is a relative thing.

## Argument Objects
When a function seems to need more than two or three arguments, it is likely that some of those arguments ought to be wrapped into a class of their own. Consider, for example, the difference between the two following declarations:
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius); 
Reducing the number of arguments by creating objects out of them may seem like cheating, but it’s not. When groups of variables are passed together, the way x and y are in the example above, they are likely part of a concept that deserves a name of its own.

## Argument Lists
Sometimes we want to pass a variable number of arguments into a function. Consider, for example, the String.format method:

```java
String.format("%s worked %.2f hours.", name, hours);
```

If the variable arguments are all treated identically, as they are in the example above, then they are equivalent to a single argument of type List . By that reasoning, String.format is actually dyadic. Indeed, the declaration of String.format as shown below is clearly dyadic.
public String format(String format, Object... args)
So all the same rules apply. Functions that take variable arguments can be monads, dyads, or even triads. But it would be a mistake to give them more arguments than that.

```java
void monad(Integer... args);
void dyad(String name, Integer... args);
void triad(String name, int count, Integer... args);
```

## Verbs and Keywords
Choosing good names for a function can go a long way toward explaining the intent of the function and the order and intent of the arguments. In the case of a monad, the function and argument should form a very nice verb/noun pair. For example, write(name) is very evocative. Whatever this “name” thing is, it is being “written.” An even better name might be writeField(name) , which tells us that the “name” thing is a “field.”
This last is an example of the keyword form of a function name. Using this form we encode the names of the arguments into the function name. For example, assertEquals might be better written as assertExpectedEqualsActual(expected, actual) . This strongly mitigates the problem of having to remember the ordering of the arguments.

Have No Side Effects
Side effects are lies. Your function promises to do one thing, but it also does other hidden things. Sometimes it will make unexpected changes to the variables of its own class.
Sometimes it will make them to the parameters passed into the function or to system globals. In either case they are devious and damaging mistruths that often result in strange temporal couplings and order dependencies.
Consider, for example, the seemingly innocuous function in Listing 3-6. This function uses a standard algorithm to match a userName to a password . It returns true if they match and false if anything goes wrong. But it also has a side effect. Can you spot it?

Listing 3-6
UserValidator.java
```java
public class UserValidator 
{
	private Cryptographer cryptographer;
	public boolean checkPassword(String userName, String password) 
	{
		User user = UserGateway.findByName(userName);
		if (user != User.NULL) 
		{
			String codedPhrase = user.getPhraseEncodedByPassword();
			String phrase = cryptographer.decrypt(codedPhrase, password);
			if ("Valid Password".equals(phrase)) 
			{
				Session.initialize();
				return true;
			}
		}
	return false;
	}
}
```

The side effect is the call to Session.initialize() , of course. The checkPassword function, by its name, says that it checks the password. The name does not imply that it initializes the session. So a caller who believes what the name of the function says runs the risk of erasing the existing session data when he or she decides to check the validity of the user. 
This side effect creates a temporal coupling. That is, checkPassword can only be called at certain times (in other words, when it is safe to initialize the session). If it is called out of order, session data may be inadvertently lost. Temporal couplings are con- fusing, especially when hidden as a side effect. If you must have a temporal coupling, you should make it clear in the name of the function. In this case we might rename the function checkPasswordAndInitializeSession , though that certainly violates “Do one thing.”

## Output Arguments
Arguments are most naturally interpreted as inputs to a function. If you have been pro- gramming for more than a few years, I’m sure you’ve done a double-take on an argument that was actually an output rather than an input. For example: appendFooter(s);

Does this function append s as the footer to something? Or does it append some footer to s ? Is s an input or an output? It doesn’t take long to look at the function signature and see:

public void appendFooter(StringBuffer report) This clarifies the issue, but only at the expense of checking the declaration of the function. Anything that forces you to check the function signature is equivalent to a double-take. It’s a cognitive break and should be avoided.

In the days before object oriented programming it was sometimes necessary to have output arguments. However, much of the need for output arguments disappears in OO languages because this is intended to act as an output argument. In other words, it would be better for appendFooter to be invoked as report.appendFooter(); In general output arguments should be avoided. If your function must change the state of something, have it change the state of its owning object.

Command Query Separation
Functions should either do something or answer something, but not both. Either your function should change the state of an object, or it should return some information about that object. Doing both often leads to confusion. Consider, for example, the following function:
public boolean set(String attribute, String value); 
This function sets the value of a named attribute and returns true if it is successful and false if no such attribute exists. This leads to odd statements like this:
if (set("username", "unclebob"))...
Imagine this from the point of view of the reader. What does it mean? Is it asking whether the “ username ” attribute was previously set to “ unclebob ”? Or is it asking whether the “ username ” attribute was successfully set to “ unclebob ”? It’s hard to infer the meaning from the call because it’s not clear whether the word “ set ” is a verb or an adjective. The author intended set to be a verb, but in the context of the if statement it feels like an adjective. So the statement reads as “If the username attribute was previously set to unclebob ” and not “set the username attribute to unclebob and if that worked then. . . .” We could try to resolve this by renaming the set function to setAndCheckIfExists , but that doesn’t much help the readability of the if statement. The real solution is to separate the command from the query so that the ambiguity cannot occur.

```java
if (attributeExists("username")) {
    setAttribute("username", "unclebob");
    ...
}
```

## Prefer Exceptions to Returning Error Codes
Returning error codes from command functions is a subtle violation of command query separation. It promotes commands being used as expressions in the predicates of if statements.

```java
if (deletePage(page) == E_OK)
```

This does not suffer from verb/adjective confusion but does lead to deeply nested structures. When you return an error code, you create the problem that the caller must deal with the error immediately.

```java
if (deletePage(page) == E_OK) 
{
	if (registry.deleteReference(page.name) == E_OK) 
	{
		if (configKeys.deleteKey(page.name.makeKey()) == E_OK)
		{
			logger.log("page deleted");
		} 
	else
	{
		logger.log("configKey not deleted");
	}
	}
	else
	{
		logger.log("deleteReference from registry failed");
	}
	
} 
else
{
	logger.log("delete failed");
	return E_ERROR;
}
```

On the other hand, if you use exceptions instead of returned error codes, then the error processing code can be separated from the happy path code and can be simplified:

```java
try
{
   deletePage(page);
   registry.deleteReference(page.name);
   configKeys.deleteKey(page.name.makeKey());
}
catch (Exception e) 
{
    logger.log(e.getMessage());
}
```

## Extract Try/Catch Blocks
Try/catch blocks are ugly in their own right. They confuse the structure of the code and mix error processing with normal processing. So it is better to extract the bodies of the try and catch blocks out into functions of their own.
plies that there is some class or enum in which all the error codes are defined.

```java
public enum Error 
{
    OK,
    INVALID,
    NO_SUCH,
    LOCKED,
    OUT_OF_RESOURCES,
    WAITING_FOR_EVENT;
}
```

Classes like this are a dependency magnet; many other classes must import and use
them. Thus, when the Error enum changes, all those other classes need to be recompiled
and redeployed. 11 This puts a negative pressure on the Error class. Programmers don’t want
to add new errors because then they have to rebuild and redeploy everything. So they reuse
old error codes instead of adding new ones.
When you use exceptions rather than error codes, then new exceptions are derivatives of
the exception class. They can be added without forcing any recompilation or redeployment. 12
Don’t Repeat Yourself 13
Look back at Listing 3-1 carefully and you
will notice that there is an algorithm that
gets repeated four times, once for each of
the SetUp , SuiteSetUp , TearDown , and
SuiteTearDown cases. It’s not easy to spot
this duplication because the four instances
are intermixed with other code and aren’t
uniformly duplicated. Still, the duplication
is a problem because it bloats the code and
will require four-fold modification should the algorithm ever have to change. It is also a four-fold opportunity for an error of omission.
This duplication was remedied by the include method in Listing 3-7. Read through that code again and notice how the readability of the whole module is enhanced by the reduction of that duplication.
Duplication may be the root of all evil in software. Many principles and practices have been created for the purpose of controlling or eliminating it. Consider, for example, that all of Codd’s database normal forms serve to eliminate duplication in data. Consider also how object-oriented programming serves to concentrate code into base classes that would otherwise be redundant. Structured programming, Aspect Oriented Programming, Component Oriented Programming, are all, in part, strategies for eliminating duplication. It would appear that since the invention of the subroutine, innovations in software development have been an ongoing attempt to eliminate duplication from our source code.

## Structured Programming
Some programmers follow Edsger Dijkstra’s rules of structured programming. 14 Dijkstra said that every function, and every block within a function, should have one entry and one exit. Following these rules means that there should only be one return statement in a function, no break or continue statements in a loop, and never, ever, any goto statements.While we are sympathetic to the goals and disciplines of structured programming, those rules serve little benefit when functions are very small. It is only in larger functions that such rules provide significant benefit.
So if you keep your functions small, then the occasional multiple return , break , or continue statement does no harm and can sometimes even be more expressive than the single-entry, single-exit rule. On the other hand, goto only makes sense in large functions, so it should be avoided.

## How Do You Write Functions Like This?
Writing software is like any other kind of writing. When you write a paper or an article, you get your thoughts down first, then you massage it until it reads well. The first draft might be clumsy and disorganized, so you wordsmith it and restructure it and refine it until it reads the way you want it to read.
When I write functions, they come out long and complicated. They have lots of indenting and nested loops. They have long argument lists. The names are arbitrary, and there is duplicated code. But I also have a suite of unit tests that cover every one of those clumsy lines of code. 
So then I massage and refine that code, splitting out functions, changing names, elim- inating duplication. I shrink the methods and reorder them. Sometimes I break out whole classes, all the while keeping the tests passing.
In the end, I wind up with functions that follow the rules I’ve laid down in this chapter.
I don’t write them that way to start. I don’t think anyone could.

Conclusion
Every system is built from a domain-specific language designed by the programmers to describe that system. Functions are the verbs of that language, and classes are the nouns. This is not some throwback to the hideous old notion that the nouns and verbs in a require- ments document are the first guess of the classes and functions of a system. Rather, this is a much older truth. The art of programming is, and has always been, the art of language design. 
Master programmers think of systems as stories to be told rather than programs to be written. They use the facilities of their chosen programming language to construct a much richer and more expressive language that can be used to tell that story. Part of that domain-specific language is the hierarchy of functions that describe all the actions that take place within that system. In an artful act of recursion those actions are written to use the very domain-specific language they define to tell their own small part of the story.
This chapter has been about the mechanics of writing functions well. If you follow the rules herein, your functions will be short, well named, and nicely organized. But never forget that your real goal is to tell the story of the system, and that the functions you write need to fit cleanly together into a clear and precise language to help you with that telling.


Listing 3-7
SetupTeardownIncluder.java
```java
package fitnesse.html;
import fitnesse.responders.run.SuiteResponder;
import fitnesse.wiki.*;

public class SetupTeardownIncluder {
	private PageData pageData;
	private boolean isSuite;
	private WikiPage testPage;
	private StringBuffer newPageContent;
	private PageCrawler pageCrawler;

	public static String render(PageData pageData) throws Exception 
	{
		return render(pageData, false);
	}

	public static String render(PageData pageData, boolean isSuite) throws Exception 
	{
		return new SetupTeardownIncluder(pageData).render(isSuite);
	}

	private SetupTeardownIncluder(PageData pageData)
	{
		this.pageData = pageData;
		testPage = pageData.getWikiPage();
		pageCrawler = testPage.getPageCrawler();
		newPageContent = new StringBuffer();
	}

	private String render(boolean isSuite) throws Exception 
	{
		this.isSuite = isSuite;
		if (isTestPage())
			includeSetupAndTeardownPages();
		return pageData.getHtml();
	}

	private boolean isTestPage() throws Exception
	{
		return pageData.hasAttribute("Test");
	}

	private void includeSetupAndTeardownPages() throws Exception
	{
		includeSetupPages();
		includePageContent();
		includeTeardownPages();
		updatePageContent();
	}

	private void includeSetupPages() throws Exception 
	{
		if (isSuite)
			includeSuiteSetupPage();
		includeSetupPage();
	}
	private void includeSuiteSetupPage() throws Exception 
	{
		include(SuiteResponder.SUITE_SETUP_NAME, "-setup");
	}

	private void includeSetupPage() throws Exception
	{
		include("SetUp", "-setup");
	}

	private void includePageContent() throws Exception 
	{
		newPageContent.append(pageData.getContent());
	}

	private void includeTeardownPages() throws Exception 
	{
		includeTeardownPage();
		if (isSuite)
			includeSuiteTeardownPage();
	}

	private void includeTeardownPage() throws Exception 
	{
		include("TearDown", "-teardown");
	}

	private void includeSuiteTeardownPage() throws Exception 
	{
		include(SuiteResponder.SUITE_TEARDOWN_NAME, "-teardown");
	}

	private void updatePageContent() throws Exception 
	{
		pageData.setContent(newPageContent.toString());
	}

	private void include(String pageName, String arg) throws Exception 
	{
		WikiPage inheritedPage = findInheritedPage(pageName);
		if (inheritedPage != null) {
		String pagePathName = getPathNameForPage(inheritedPage);
		buildIncludeDirective(pagePathName, arg);
	}

	}
	
	private WikiPage findInheritedPage(String pageName) throws Exception 
	{
		return PageCrawlerImpl.getInheritedPage(pageName, testPage);
	}
	
	private String getPathNameForPage(WikiPage page) throws Exception 
	{
		WikiPagePath pagePath = pageCrawler.getFullPath(page);
		eturn PathParser.render(pagePath);
	}
	
	private void buildIncludeDirective(String pagePathName, String arg) 
	{
		newPageContent
			.append("\n!include ")
			.append(arg)
			.append(" .")
			.append(pagePathName)
			.append("\n");
	}
}
```
