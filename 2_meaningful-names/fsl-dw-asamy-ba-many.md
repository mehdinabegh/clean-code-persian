# فصل دو - اسامی با معنی

### مقدمه 

 اسم ها همه جای نرم افزار وجود دارند. ما متغیر ها، تابع ها، آرگومان ها، کلاس ها و پکیج هایمان را نام گذاری میکنیم. ما فایل های سورس و دایرکتوری هایی که آنها را شامل میشوند را نام گذاری میکنیم. ما حتی فایل های jar و war و ear را نامگذاری میکنیم. ما نامگذاری میکنیم، نامگذاری میکنیم و نامگذاری میکنیم. از آنجایی که این کار را خیلی زیاد انجام میدهیم ، بهتر است که آن را با شیوه درست دهیم. آنچه که در ادامه میخوانید،قوانینی ساده برای خلق اسم های خوب هستند. 

### استفاده از اسم های بیان کننده منظور \(Intention-Revealing Names\)

 کاملا واضح است که اسم ها باید منظور شما را بازتاب دهند. چیزی که ما میخواهیم به شما بگوییم این است که ما در این قضیه کاملا جدی هستیم. انتخاب کردن اسم های خوب زمانبر است ولی زمان بیشتری از آنچه میگیرد را ذخیره میکند. پس به اسم هایتان توجه کنید و هر وقت اسم های بهتری یافتید، آنها را عوض کنید. هر کسی که کد شما را بخواند \(شامل خودتان\) از انجام این کار خوشحال میشود. اسم یک متغیر، تابع یا کلاس، باید به تمام سوال های بزرگ پاسخ دهد. اسم باید بگوید که چرا وجود دارد، چه میکند و چگونه استفاده میشود.اگر اسمی به کامنت نیاز داشته باشد،پس منظور خود را نمیرساند.
```c
int d; // elapsed time in days 
```
اسم d در کد بالا هیچ منظوری را نمیرساند. این اسم احساس گذشت روز ها را ایجاد نمیکند. ما باید اسمی انتخاب کنیم که مشخص کند چه چیزی در حال اندازه گیری شدن است و واحد و مقیاس آن اندازه گیری چیست. 
```c
int elapsedTimeInDays;

int daysSinceCreation;

int daysSinceModification;

int fileAgeInDays; 
```
انتخاب اسم هایی که منظور ما را القا میکند ، فهمیدن و تغییر دادن کد را راحت تر میکند. میتوانید بگویید کد زیر چه میکند؟
```c
 Public List getThem() { 

        List list1 = new ArrayList; 

                For (int[] x : theList\) 

                        If (x[0] == 4) 

                                List1.add(x); 

         Return list1;
```
چرا گفتن کاری که این کد انجام میدهد سخت است؟ اینجا هیچ کد پیچیده ای وجود ندارد. فاصله ها و تورفتگی ها کاملا معقول هستند. در کل سه متغیر و دو ثابت استفاده شده است. اینجا هیچ کلاس عجیب یا متد پیچیده ای وجود ندارد، فقط یک لیست از آرایه ها وجود دارد. مشکل سادگی کد نیست ، صراحت کد است؛ میزان صریح بودن کد در بیان منظور خودش. صریح بودن کد نیازمند این است که ما بتوانیم به سوال های نظیر این ها پاسخ دهیم:



1. چه چیز هایی در theList وجود دارد؟     
2. اهمیت عضو صفرم در theList چیست؟  
3.  اهمیت مقدار 4 چیست؟     
4.  چگونه از لیستی که برگشت داده میشود استفاده کنم؟     

جواب سوال ها در مثال قبل قابل تشخیص نیستند، ولی باید مشخص شوند. فرض کنید ما داریم روی یک بازی مین روبی کار میکنیم. ما میدانیم که صفحه بازی یک لیست از سلول هاست که با عنوان theList نمایش داده میشود. بیایید نام آن را به gameBoard تغییر دهیم. هر سلول در صفحه توسط یک آرایه ساده نشان داده میشود. همچنین این را میدانیم که مقدار صفرم ، وضعیت سلول است و اینکه وضعیت 4 یعنی "پرچم گذاری شده". بیایید با دادن اسم های این کانسپت ها کد را به شکل قابل توجهی بهبود دهیم:
```c
Public List getFlaggedCells() { 

        List flaggedCells = new ArrayList(); 

        For (int[] cell : gameBoard) 

                If (cell[STATUS_VALUE] == FLAGGED) 

                        flaggedCells.add(cell); 

        return flaggedCells; 

}
```
دقت کنید که سادگی کد تغییری نکرده است و هنوز هم همان تعداد مقادیر ثابت و متغیر وجود دارد، دقیقا با همان تعداد تورفتگی و بیرون زدگی. ما میتوانیم پا را فراتر بگذاریم و به جای یک آرایه از اعداد ، یک کلاس ساده برای سلول ها بنویسیم.این کلاس میتواند یک تابع را شامل شود \(که منظور خود را به درستی به نمایش میگذارد و آن را isFlagged می نامیم\) که باعث میشود اعداد عجیب از کد حذف شوند:
```c
public List getFlaggedCells() { 

        List flaggedCells = new ArrayList(); 

        for (Cell cell : gameBoard) 

                if (cell.isFlagged()) 

                        flaggedCells.add(cell); 

        return flaggedCells; 

}
```
با این تغییرات ساده، دیگر فهمیدن اینکه چه کاری در حال انجام است سخت نیست. این قدرت انتخاب کردن اسم های خوب است.

### خودداری از دادن اطلاعات اشتباه

برنامه نویس ها باید از به جا گذاشتن اطلاعات اشتباه که معنی کد را خراب میکنند خودداری کنند. ما باید از کلماتی که مفهوم آنها با منظور ما فاصله زیادی دارد دوری کنیم. برای مثال، کلمات hp ، aix و sco نام های ضعیفی برای متغیر ها هستند زیرا از اسامی یونیکس پلتفرم هستند. حتی اگر شما در حال نوشتن یک Hypotenuse هستید و hp مخفف خوبی برای آن به نظر میرسد، ممکن است اطلاعات ناسازگار در کد به جا بگذارید. هرگز به یک لیست از اکانت ها اسم accountList را ندهید زیرا آن واقعا یک لیست است ولی کلمه List  به معنی چیزی است که به برنامه نویس ها اختصاص دارد. اگر یک کانتینر اکانت را نگهداری میکند، به این معنی نیست که یک List است، این ممکن است به اطلاعات غلط منجر شود. پس accountGroup یا bunchOfAccounts یا فقط accounts اسم های بهتری هستند. 

از استفاده از اسم هایی که تفاوت های کوچکی با هم دارند خودداری کنید. چه تضمینی وجود دارد که بعدا دو شی با نام های XYZControllerForEfficientHandlingOfStringsin و XYZControllerForEfficientStorageOfStrings با هم اشتباه گرفته نشوند؟ هر دو اسم شکل یکسانی دارند. در انتخاب اسم به تلفظ آن دقت کنید. استفاده از اسم با تلفظ اشتباه نوعی اطلاعات غلط است. به کمک محیط های مدرن جاوا ما میتوانیم از تکمیل شدن خودکار کد ها لذت ببریم. ما چند حرف تایپ میکنیم و دکمه ای را فشار میدهیم که لیستی از اسامی قابل استفاده برای تکمیل کلمه را به ما نشان میدهد.بسیار مفید است که نامهای بسیار مشابه به ترتیب حروف الفبا مرتب شوند.تفاوت ها را آشکار میکند. 

یک مثال از نام هایی که اطلاعات غلط میدهند، نام هایی هستند که از حروفی استفاده میکنند که شبیه حروف دیگر هستند. مثلا حرف I و L ، اگر به صورت I و l نوشته شوند،مطمئنا اشتباه گرفته میشوند. همچنین حرف lو1 نیز ممکن است اشتباه گرفته شوند.
```c
int a = l;

 if(0==1) 

        a=01; 

else 

        l = 01;
```
شاید فکر کنید این اتفاق محال است ؛ اما ما کد هایی را بررسی کرده ایم که چنین مواردی در آنها به وفور وجود داشت. در وهله اول نویسنده پیشنهاد میکند که فونت متن را تغییر دهید که باعث آشکار شدن بهتر تفاوت ها میشود، اما این راه حل باید به توسعه دهندگان آینده به صورت شفاهی یا کتبی منتقل شود. بهترین راه حل یک تغییر نام ساده است.

### تفاوت های با معنی ایجاد کنید

برنامه نویس ها با نوشتن کد های کامپایلر پسند برای خودشان مشکل ایجاد میکنند. برای مثال شما نمیتوانید از یک اسم در دو متغیر مختلف استفاده کنید،شما مجبورید یکی از اسم ها را کمی تغییر دهید. گاهی اوقات اینکار را با تغییر دادن املای کلمات انجام می دهید، در اینصورت ممکن است تصحیح خطاهای املایی باعث مشکل در کامپایل نرم افزار شود. افزودن اعداد یا حروف اضافه هرگز کافی نیست. اگر نام ها باید متفاوت باشند، پس معنی آنها نیز باید فرق کند.

 اسامی شامل سری اعداد \(a1,a2,…,aN\) با نام گذاری اصولی در تضاد هستند. این اسم ها اطلاعات غلط نمیدهند،چون اصلا اطلاعاتی نمیدهند و کاملا بی معنی هستند. اینها هیچ سرنخی از منظور شما به دیگران نمیدهند. به مثال نگاه کنید:
```c
Public static void copyChars(char a1[], char a2[] { 

        For (int i=0; i&lt;a1.length; i++) { 

                A2\[[i] = a1[i];

         }

 }
```
این تابع خواناتر میشود، اگر از اسم های source و destination در آرگومان ها استفاده کنیم. 

حروف اضافه\(noise words\) موارد بی معنی دیگری هستند. تصور کنید شما کلاسی به اسم Product دارید. اگر شما کلاس های دیگری با اسم های ProductInfo یا ProductData بسازید، شاید اسم های مختلفی انتخاب کرده باشید اما در معنای آنها تفاوتی وجود ندارد. کلمات Info و Data کلمات اضافه هستند، مثل a, an و the . دقت کنید که استفاده از این پیشوند ها مشکلی ندارد، مثلا شما میتوانید از a برای همه متغیر های محلی و از the برای همه آرگومان های توابعتان استفاده کنید. در حقیقت مشکل از جایی شروع میشود که شما یک متغیر را theZork بنامید، فقط به این دلیل که متغیر دیگری به نام Zork دارید. حروف اضافه ،زائد هستند. کلمه Variabale هیچوقت نباید در اسم یک متغیر نمایان شود. واژه table نباید در اسم یک Table نشان داده شود. چرا فکر میکنید NameString بهتر از Name است؟ آیا ممکن است Name یک عدد اعشاری باشد؟اگر جواب بله است،پس شما کل قوانین را زیر سوال برده اید! 

فرض کنیدکلاسی به اسم Customer و کلاسی دیگر به اسم CustomerObject دارید.از اختلاف این دو اسم چه چیزی دستگیرتان میشود؟ کدام یک از این دو اسم ، بهتر میتواند تاریخچه پرداخت های یک مشتری را نشان دهد؟ در مثال های زیر استفاده مناسب از حروف اضافه را میبینید.
```c
getActiveAccount(); 

getActiveAccounts(); 

getActiveAccountInfo();
```
برنامه نویس به راحتی میفهمد که کدام تابع را نیاز دارد.

دقت کنید که حروف اضافه چه تغییری در اسم متغیر شما میدهند. تفاوت متغیر moneyAmount از money غیرقابل تشخیص است. تفاوت customerInfo از customer نیز همینطور.accountData از account و theMessage از message. اسامی قابل تشخیص باید تفاوت خود را به خواننده نشان دهند.

### از اسم های قابل تلفظ استفاده کنید

انسان ها در استفاده از کلمات ماهرند. قسمت های مشخصی از مغز ما به درک مفهوم کلمات اختصاص داده شده اند.مفهوم کلمات ارتباط مستقیمی با تلفظ آنها دارد. مغز ما کلمات را با توجه به تلفظ آنها درک میکند، نه نوشتار آنها. بنابراین، بهتر است از اسم های قابل تلفظ استفاده کنید. اگر نمیتوانید یک اسم را تلفظ کنید، نمیتوانید درباره آن بحث کنید،بدون اینکه مثل یک احمق صدا در بیاورید! "Well,over here on the bee cee arr three cee enn tee we have a pee ess zee kyew int, see?" میتوانید این متن را بخوانید؟ فهمیدید که این موضوع مهم است، چون برنامه نویسی یک فعالیت اجتماعی است. یک کمپانی در برنامه خود ، مفهومی به نام genymdhms دارد \(generation date, year, month, day, hour, minute and second \). آنها برای اینکه این اسم را به خاطر بسپارند، قدم زنان میگویند "gen why emm dee aich emm ess". من عادت مسخره ای دارم که در آن هر چیزی را به همان شکلی که نوشته شده میخوانم،پس من شروع کردم به گفتن "gen-yah-mudda-hims." .بعدها این مفهوم توسط جمعی از طراحان و آنالیزور ها به همین اسم نامیده شد، و ما هنوز هم به درآوردن این صدای احمقانه ادامه میدهیم. از آنجایی که این برای ما مثل یک شوخی به حساب می آمد، برای ما بانمک بود. بانمک باشد یا نباشد، ما داریم اسم گذاری ضعیف را تحمل میکنیم. برنامه نویس های جدید ما نیازمند این هستند که این مفهوم را برایشان توضیح دهیم و آنها در مورد دلیل در آوردن این صداهای احمقانه به جای استفاده از قوانین صریح و کلمات بامعنای انگلیسی صحبت میکنند.مقایسه کنید:
```c
class DtaRcrd102 {

        private Date genymdhms;

        private Date modymdhms;

        private final String pszqint = “102”;

        /\* _...  \*_/

}
```
 با
```c
class Customer { 

        private Date generationTimestamp; 

        private Date modificationTimestamp;

         private final String recordId = "102"; 

        /\* _...  \*_/ 

};
```
 مکالمه عاقلانه اکنون امکان پذیر است:

 “Hey, Mikey, take a look at this record! The generation timestamp is set to tommorrow’s date! How can that be?"

### از اسامی قابل جستجو استفاده کنید

اسامی تک حرفی و ثابت های عددی این مشکل را دارند که در متن قابل پیداکردن نیستند.احتمالا پیدا کردن MAX\_CLASSES\_PER\_STUDENT در یک متن راحت است، اما مثلا پیدا کردن عدد 7 در یک متن طولانی، سخت و زمان بر است. جستجو ها ممکن است اعداد دیگری را نیز برای شما پیدا کنند، مثل قسمت های عددی اسم فایل ها،ثابت های توابع دیگر و در عبارات گوناگونی که از اعداد با اهداف متفاوتی استفاده میکنند. وقتی یک عدد طولانی استفاده میکنید، ممکن است شخصی رقم هایی را سهوا جابجا کند و همزمان این عدد از جستجوی شما فرار میکند. برای مثال، حرف e ضعیف ترین حرف ممکن برای نامگذاری یک متغیر برای برنامه نویسی است که نیازمند جستجو کردن است. چون این حرف پرکاربردترین حرف در زبان انگلیسی است و در هر عبارتی یافت میشود و باعث میشود حین جستجو هر متنی را که در هر برنامه ای وجود دارد ببینید. 

در این راستا در هر کدی، نام های طولانی تر، بر نام های کوتاه تر برتری دارند و نام های قابل جستجو بر ثابت ها. به نظر من نام های تک حرفی تنها میتوانند به عنوان متغیر های محلی در متد های کوتاه استفاده شوند. **طول یک نام باید با دامنه استفاده آن مطابقت داشته باشد.** اگر ممکن است از یک متغیر در چندین محل از یک کد استفاده کنید، ضروری است که یک اسم جستجو-دوست \(search-friendly\) داشته باشید. یکبار دیگر مقایسه کنید	
```c
 For (int j=0; j&lt;34; j++) {

         S+= (t[j]*4)5;

 }
```
 با
```c
 int realDaysPerIdealDay = 3;

 cons tint WORK_DAYS_PER_WEEK = 5;

 int sum = 0;

 for (int j=0; j&lt;NUMBER_OF_TASKS; j++) {

         int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;

         int realTaskWeeks = (realdays  WORK_DAYS_PER_WEEK);

         sum += realTaskWeeks;

 }
```
به sum دقت کنید، این اسم کاملا مناسب نیست، اما حداقل قابل جستجو کردن است. نوشتن کد با این نامگذاری ها شاید کمی طولانی تر باشد ، اما این را هم در نظر داشته باشید که پیدا کردن WORK\_DAYS\_PER\_WEEK راحت است زیرا فقط 5 بار از آن استفاده شده.

### از رمزگذاری خودداری کنید

ما به اندازه کافی از رمزنگاری ها برای زیاد کردن دردسرمان استفاده میکنیم، لطفا آن را از چیزی که هست بیشتر نکنید.نام های رمزگذاری شده به سادگی قابل تلفظ نیستند و برای توسعه دهندگان جدید دردسر ایجاد میکنند،زیرا هر کارمند جدید باید زبان رمزنگاری شما را بیاموزد که خود باعث فشار روحی زیادی است.

### نمادگذاری مجارستانی

 در روزهای قبل، وقتی ما روی زبان های name-length-challenged کار میکردیم،این امر را با پشیمانی و بخاطر ضرورت نقض کردیم. Fortran رمزگذاری را اجبار میکرد. نسخه های اولیه BASIC فقط یک حرف به اضافه یک رقم را مجاز میکرد. HungarianNotation \(HN\) این مرحله را به سطح کاملا جدیدی رساند.

و HN در Windows C API بسیار مهم به حساب می آمد،هنگامی که همه چیز یک دسته صحیح یا یک نشانگر طولانی یا یک Voidpointer یا یکی از چندین نوع "رشته" \(با کاربرد ها و ویژگی های مختلف\) بود. کامپایلر نوع متغیر های ورودی را بررسی نمی کرد، بنابراین برنامه نویسان برای کمک به آن در به خاطر سپردن نوع داده ها نیاز به عصا داشتند. 

در زبان های مدرن سیستم های بسیار غنی تری داریم و کامپایلر ها می توانند به خاطر بیاورند که هر داده ای از چه نوع است.گذشته از این موضوع،گرایش به کلاسهای کوچکتر و عملکرد های کوتاه تر نیز وجود دارد تا افراد معمولا بتوانند نقطه ی اعلام هر متغیری که میخواهند را ببینند. برنامه نویسان جاوا نیازی به رمزگذاری ندارند.نوع اشیا مشخص است و محیط های ویرایش به گونه ای پیشرفت کرده اند که یک خطای نوع را قبل از اینکه بتوانید برنامه را کامپایل کنید،تشخیص میدهند. 

### پیشوندهای عضو

 شما دیگر نیازی به پیشوند  m\_ در متغیر های عضو\(member variable\) ندارید. کلاس ها و توابع شما باید به قدری کوچک باشند که نیازی به آنها نباشد و شما باید از یک محیط ویرایش استفاده کنید که اعضا را برجسته و یا رنگی و آنها را متمایز کند.
```c
 public class Part {

         private String m_dsc; // The textual description

         void setName(String name) {

                 M_dsc = name;

         }

 }
```
```c
 public class Part {

         String description;

         void setDescription(String description) {

                 this.description = description;

         }

}
```
 علاوه بر این،افراد به سرعت یاد میگیرند که پیشوند \( یا پسوند\) را نادیده بگیرند تا بخش بامعنای نام را ببینند. هرچه آن را بیشتر بخوانند، کمترthe را میبینند.در نهایت پیشوندها به صورت تصادفی دیده می شوند و نشانگر کد قدیمی تر هستند.

### واسط ها \(interface\) و  پیاده سازی ها

اینها گاهی اوقات مورد خاصی برای رمزگذاری  است. به عنوان مثال ، می گویند شما در حال ساخت یک ABSTRACT  FACTORY برای ایجاد شکل ها هستید. این factory یک واسط خواهد بود و توسط یک کلاس concrete \(کلاسی که همه متد ها را پیاده سازی کند\) پیاده سازی خواهد شد. چه اسمی باید برای انها بگذاریم؟ IShapeFactions و ShapeFective؟ من ترجیح می دهم واسط را بدون آراستگی رها کنم .پیشوند i ، که در میان میراث\(legacy\)  این روز ها معمول است ، در بهترین حالت حواس پرتی و در بدترین اطلاعات اضافی است. من نمی خواهم که کاربرانم بدانند که من interface خود را به آنها ارائه می کنم. من فقط می خواهم آنها بدانند که این یک shape factoryاست. بنابراین اگر من باید  یا interface یا implementation را رمزگذاری کنم ، implementation را انتخاب می کنم. نام ان را  ShapeFactoryImp ، یا حتی CShapeFective ناخوشایند  بگذارید، رمزگذاری واسط ها ضروری است.

### از نگاشت ذهنی خودداری کنید

  
		@page { size: 8.27in 11.69in; margin: 0.79in }  
		p { margin-bottom: 0.1in; direction: ltr; line-height: 115%; text-align: left; orphans: 2; widows: 2; background: transparent }  
		a:link { color: \#000080; so-language: zxx; text-decoration: underline }  
		a:visited { color: \#800000; so-language: zxx; text-decoration: underline }  
	

 خوانندگان نباید مجبور باشند که نام انتخابی شما را در ذهنشان به نام دیگری که از قبل می شناسند ترجمه کنند. این مشکل معمولاً ناشی از انتخابی است که نه از دامنه اصطلاحات مسئله استفاده کرده است و نه از دامنه واژه های راه حل.

 این مشکل مربوط به نام متغیرهای تک حرفی است. مطمئناً یک دامنه نام برای شمارشگر حلقه ای که کوچک باشد و هیچ نام دیگری با آن به تناقض نرسد، i یا j یا k است \(هرچند هیچگاه l نامگذاری نمی شود\). دلیل این امر آن است که این نامهای تک حرفی از قدیم برای شمارشگر حلقه استفاده شده اند. با این وجود ، در بیشتر زمینه های دیگر ، یک نام تک حرفی انتخاب بدی است. این انتخاب تنها یک جایگزین است که خواننده باید در ذهن خود آن را به مفهومی واقعی نگاشت کند. دلیلی بدتر از اینکه a و b قبلاً استفاده شده بود برای استفاده از نام c نمی تواند وجود داشته باشد.

 به طور کلی برنامه نویسان افراد بسیار باهوشی هستند. افراد باهوش گاهی دوست دارند با نشان دادن توانایی های ذهنی خود ، هوشمندی خود را به معرض نمایش بگذارند. از این گذشته ، اگر با اطمینان بتوانید به یاد داشته باشید که r نسخه کوتاه شده از آدرس url با میزبان و شمای حذف شده است ، پس مشخصا باید بسیار باهوش باشید.

 یک تفاوت بین یک برنامه نویس هوشمند و یک برنامه نویس حرفه ای در این است که حرفه ای می فهمد که شفافیت پادشاه است. حرفه ای ها به خوبی از توانایی های خود برای نوشتن کدی که دیگران می توانند آن را درک کنند استفاده می کنند.

### نام کلاس ها

 کلاسها و اشیاء باید دارای نام یا عبارت اسمی مانند Customer ، WikiPage ، Account و AdressParser باشند. از کلماتی مانند Manager ، Processor ، Data یا Info برای نام کلاس خودداری کنید. **نام کلاس نباید یک فعل باشد.**

###  ****نام متدها

 ![](file:///tmp/lu5330u932g1.tmp/lu5330u932gm_tmp_4419b36f46173101.png) اسم متدها باید دارای فعل یا عبارت فعلی مانند postPayment ، DeletePage یا save باشند. Accessorها، mutator ها و predicate ها را باید با مقدار خودشان نامگذاری کرد و با در نظر گرفتن استانداردهای javabean، با پیشوندهای get، set و is نام گذاری کرد.
```c
String name = employee.getName();

customer.setName("mike")

if (paychech.isPosted()) ...
```
 زمانی که متدهای سازنده(cunstructor) زیاد شوند، از factory method های static با نام هایی که متغیرها را توصیف کنند استفاده کنید. به عنوان مثال،
```c
Complex FulcrumPoint = Complex.FromRealNumber(23.0);
```
 به طوور کلی بهتر است از :
```c
Complex FulcrumPoint = new Complex(23.0);
```
 در نظر بگیرید که با private کردن متدهای سازنده مربوطه ، بر استفاده از آنها تاکید کنید.

###  بانمک نباشید

 اگر نام ها خیلی هوشمندانه باشند ، فقط در ذهن افرادی که از نظر شوخ طبعی به نویسنده شبیه هستند، و فقط تا زمانی که این افراد این شوخی را به خاطر می آورند، ماندگار می شوند. آیا آنها می دانند تابعی به نام HolyHandGrenade قرار است چه کاری انجام دهد؟ مطمئنا ، بانمک است ، اما شاید در این حالت DeleteItems نام بهتری باشد. شفافیت را در برابر سرگرمی انتخاب کنید.

 بامزگی در کد اغلب به شکل محاوره یا زبان عامیانه ظاهر می شود. به عنوان مثال ، از نام whack \(\) به جای kill\(\) استفاده نکنید. از شوخی های خاص در یک فرهنگ مانند eatMyShors \(\) به جای abort\(\) استفاده نکنید.

 آن چیزی را بگویید که منظورتان است. منظورتان همان چیزی است که می گویید.

### برای هر مفهوم یک کلمه انتخاب کنید

 یک کلمه را برای یک مفهوم مجزا انتخاب کنید و به آن بچسبید. به عنوان مثال ، fetch ، retrieve و get به عنوان نام متد های معادل در کلاسهای مختلف گیج کننده است. چگونه به یاد خواهید آورد که نام کدام متد در کدام کلاس ذکر شده است؟ متأسفانه ، شما اغلب باید به خاطر داشته باشید كه كدام شركت ، گروه یا فرد كتابخانه یا كلاس را نوشته است تا بتوانید به یاد آورید که كدام اصطلاح استفاده شده است. در غیر این صورت ، شما زمان بسیار زیادی را صرف مرور در header ها و نمونه کد های قبلی می کنید.

 محیطهای ویرایش مدرن مانند Eclipse و IntelliJ- سرنخهای حساس به متن\(context-sensitive clues\) مانند لیست متدهایی که می توانید در یک شی خاص فراخوانی کنید را فراهم می کنند. اما توجه داشته باشید که این لیست معمولاً comment هایی را که شما در مورد نام تابع و لیست پارامترهای خود نوشتید به شما نمی دهد. خوش شانس باشید، بتواند نام پارامترها را از توصیف توابع به شما ارائه دهد. اسامی توابع باید مستقل واضح باشند، و آنها باید سازگار باشند تا شما بتوانید متد صحیح را بدون جستجوهای اضافی انتخاب کنید.

 به همین ترتیب ، داشتن یک Controller و یک manager و یک driver در یک پایگاه کد گیج کننده است. تفاوت اساسی بین DeviceManager و ProtocolController چیست؟ چرا هر دو Controller نیستند یا هر دو manager نیستند؟ آیا آن دو واقعاً driver هستند؟ این نام ها باعث می شود که شما توقع دو شی که از نظر نوع و کلاس هایشان بسیار متفاوت هستند را داشته باشید.

 واژه نامه نامتناقض یک لطف بزرگ به برنامه نویسانی است که باید از کد شما استفاده کنند.

### با ایهام ننویسید

 از استفاده از یک کلمه برای دو منظور مختلف خودداری کنید. استفاده از یک اصطلاح یکسان برای دو ایده متفاوت در اصل یک ایهام است.

 اگر از قانون "یک کلمه برای هر مفهوم" پیروی کنید ، می بینید که بسیاری از کلاس ها مثلا یک متد add دارند. تا زمانی که لیست پارامترها و مقادیر برگشتی در متدهای مختلف add معنایی معادل داشته باشند، همه چیز خوب است.

 اما ممکن است شخص تصمیم بگیرد از کلمه add وقتی که منظورش افزودن نیست استفاده کند و تنها بخواهد سازگاری ایجاد کند. بیایید بگوییم که ما کلاسهای زیادی داریم که در آنها با اضافه کردن یا ملحق کردن دو مقدار موجود، مقدار جدیدی ایجاد میشود. حال فرض کنیم ما در حال نوشتن یک کلاس هستیم که متدی در آن است که یک پارامتر را درون یک مجموعه می گذارد. آیا باید این متد را add بنامیم؟ ممکن است به این دلیل که متدهای add دیگری داریم، سازگار به نظر برسد، اما در این حالت، مفاهیم متفاوت هستند، بنابراین باید به جای آن از اسمی مانند insert یا append استفاده کنیم. نامیدن متد جدید به add ایهام ایجاد میکند.

 هدف ما ، به عنوان نویسنده ، این است که کدهای خود را تا جایی که میتوانیم قابل درک کنیم. ما می خواهیم کد ما به جای اینکه یک مطالعه عمیق و مفهومی باشد، سریع خوانده شود. ما می خواهیم از مدل رایج کتاب های کاغذی استفاده کنیم که به موجب آن نویسنده وظیفه دارد منظور خود را شفاف سازد و نه مدل دانشگاهی كه در آن محققان وظیفه دارند مفاهیم و معانی را از مقاله ها استخراج کنند.

### از دامنه واژگان مربوط به راه حل استفاده کنید

 به یاد داشته باشید افرادی که کد شما را می خوانند ، برنامه نویس هستند. بنابراین از اصطلاحات علوم کامپیوتر، نام الگوریتم ها، نام الگوها ، اصطلاحات ریاضی و موارد دیگر استفاده کنید. عاقلانه نیست که هر نام را از دامنه صورت مسئله انتخاب کنیم زیرا ما نمی خواهیم همکاران ما مجبور به فرار از مشتری ای باشند که چون هر مفهوم را با نام دیگری میشناسد، معنی هر کلمه را می پرسد.

 نام AccountVisitor برای یک برنامه نویس که با الگوی VISITOR آشنا است بسیار پر معنی است. چه برنامه نویسی نمی داند JobQueue چیست؟ موارد فنی بسیار زیادی وجود دارد که برنامه نویسان باید انجام دهند. انتخاب نام های فنی برای آن موارد معمولاً مناسب ترین روش است.

###  از دامنه واژگان صورت مسئله استفاده کنید

 وقتی "اصطلاح برنامه نویسی" برای کاری که انجام می دهید وجود ندارد ، از دامنه صورت مسئله برای انتخاب نام استفاده کنید. حداقل برنامه نویسی که کد شما را نگهداری میکند می تواند از یک متخصص دامنه سوال کند که منظور چیست.

 جدا کردن دامنه راه حل و صورت مسئله بخشی از کار یک برنامه نویس و طراح خوب است. کدی که ارتباط بیشتری با مفاهیم مربوط به صورت مسئله دارد باید دارای نامهایی باشد که از دامنه صورت مسئله گرفته شده است.



## بخش های ترجمه نشده\(برای ترجمه متن انگلیسی را با ترجمه در گیتهاب جایگزین کنید: [https://github.com/Noah1001000/clean-code-persian](https://github.com/Noah1001000/clean-code-persian)\)

### Add Meaningful Context

 There are a few names which are meaningful in and of themselves—most are not. Instead, you need to place names in context for your reader by enclosing them in well-named classes, functions, or namespaces. When all else fails, then prefixing the name may be nec- essary as a last resort.Imagine that you have variables named firstName , lastName , street , houseNumber , city , state , and zipcode . Taken together it’s pretty clear that they form an address. But what if you just saw the state variable being used alone in a method? Would you automatically infer that it was part of an address? You can add context by using prefixes: addrFirstName , addrLastName , addrState , and so on. At least readers will understand that these variables are part of a larger structure. Of course, a better solution is to create a class named Address . Then, even the compiler knows that the variables belong to a bigger concept. Consider the method in Listing 2-1. Do the variables need a more meaningful con- text? The function name provides only part of the context; the algorithm provides the rest. Once you read through the function, you see that the three variables, number , verb , and pluralModifier , are part of the “guess statistics” message. Unfortunately, the context must be inferred. When you first look at the method, the meanings of the variables are opaque.

Listing 2-1 

Variables with unclear context. 


```c
private void printGuessStatistics(char candidate, int count)
{
	String number;
	String verb;
	String pluralModifier;
	if (count == 0)
	{
		number = "no";
		verb = "are";
		pluralModifier = "s";
	} 
	else if (count == 1)
	{ 
		number = "1";
		verb = "is";
		pluralModifier = "";
	} 
	else 
	{
		number = Integer.toString(count);
		verb = "are";
		pluralModifier = "s";
	} 
	String guessMessage = String.format( "There %s %s %s%s", verb, number, candidate, pluralModifier );
	print(guessMessage); 
}
```


The function is a bit too long and the variables are used throughout. To split the func- tion into smaller pieces we need to create a GuessStatisticsMessage class and make the three variables fields of this class. This provides a clear context for the three variables. They are definitively part of the GuessStatisticsMessage . The improvement of context also allows the algorithm to be made much cleaner by breaking it into many smaller functions. \(See Listing 2-2.\)



Listing 2-2 

Variables have a context.
```c
public class GuessStatisticsMessage
{ 
	private String number;
	private String verb;
	private String pluralModifier;
	public String make(char candidate, int count)
	{ 
		createPluralDependentMessageParts(count);
		return String.format( "There %s %s %s%s", verb, number, candidate, pluralModifier );
	} 
	private void createPluralDependentMessageParts(int count)
	{ 
		if (count == 0)
		{ 
			thereAreNoLetters();
		} 
		else if (count == 1) 
		{
			thereIsOneLetter();
		} 
		else 
		{ 
			thereAreManyLetters(count); 
		} 
	} 
	private void thereAreManyLetters(int count)
	{ 
		number = Integer.toString(count);
		verb = "are"; pluralModifier = "s";
	} 
	private void thereIsOneLetter()
	{ 
		number = "1";
		verb = "is";
		pluralModifier = "";
	} 
	private void thereAreNoLetters()
	{ 
		number = "no";
		verb = "are";
		pluralModifier = "s";
	} 
}
```


### Don’t Add Gratuitous Context

 In an imaginary application called “Gas Station Deluxe,” it is a bad idea to prefix every class with GSD . Frankly, you are working against your tools. You type G and press the com- pletion key and are rewarded with a mile-long list of every class in the system. Is that wise? Why make it hard for the IDE to help you? Likewise, say you invented a MailingAddress class in GSD ’s accounting module, and you named it GSDAccountAddress . Later, you need a mailing address for your customer con- tact application. Do you use GSDAccountAddress ? Does it sound like the right name? Ten of 17 characters are redundant or irrelevant.Shorter names are generally better than longer ones, so long as they are clear. Add no more context to a name than is necessary. The names accountAddress and customerAddress are fine names for instances of the class Address but could be poor names for classes. Address is a fine name for a class. If I need to differentiate between MAC addresses, port addresses, and Web addresses, I might consider PostalAddress , MAC , and URI . The resulting names are more precise, which is the point of all naming.

### Final Words 

The hardest thing about choosing good names is that it requires good descriptive skills and a shared cultural background. This is a teaching issue rather than a technical, business, or management issue. As a result many people in this field don’t learn to do it very well. People are also afraid of renaming things for fear that some other developers will object. We do not share that fear and find that we are actually grateful when names change \(for the better\). Most of the time we don’t really memorize the names of classes and meth- ods. We use the modern tools to deal with details like that so we can focus on whether the code reads like paragraphs and sentences, or at least like tables and data structure \(a sen- tence isn’t always the best way to display data\). You will probably end up surprising some- one when you rename, just like you might with any other code improvement. Don’t let it stop you in your tracks. Follow some of these rules and see whether you don’t improve the readability of your code. If you are maintaining someone else’s code, use refactoring tools to help resolve these problems. It will pay off in the short term and continue to pay in the long run.







### 

