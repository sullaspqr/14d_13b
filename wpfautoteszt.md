
# WPF alkalmazás tesztelésének automatizálása

Egy komplexebb alkalmazás az egyes osztályokon kívül számos grafikus felületet (UI-user interface) foglalhat magába. Nem csak a háttérben dolgozó egységek tesztelése szükséges, hanem a UI működését is szükséges tesztelni. Ennek az elvégzése fáradtságos munkával járhat, ezért szükség lehet a tesztelés automatizálására.

A windowsos grafikus felületű alkalmazások automatizált tesztelése hasonlít a böngészőben végzett tesztekhez. A felület elemeibe adatokat küldhetünk, gombra kattintást programozhatunk stb. Az egyes tesztesetekre tesztekek készíthetünk, és utána annyiszor futtathatóak, amennyiszer szükséges. A tesztek eredményéről riportot lehet készíteni stb.

A cél: Egy nagyon egyszerű, Celsius-Fahrenheit hőmérséklet átszámító program készítése, majd tesztelése automatizált teszttel, és a végén riport készítése a tesztek futásáról.

Egy új Wpf alkalmazás a kiindulópont a projekt neve legyen **WpfHomersekletAtvalto**.

A projekt létrehozása után adjunk hozzá egy új osztályt az osztály neve legyen **HomersekletAtvalto**. Erre az osztályra bízzuk a megfelelő hőmérsékletértékek kiszámítását. Két metódusa lesz, az egyik Celsius fokot vált Fahrenheit-re, a másik Fahrenheit fokot Celsiusra. Statikus osztály lesz, így nem lesz szükség külön példányosításra.

Az osztály kódja,metódusai:
```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace WpfHomersekletAtvalto
{
    public static class HomersekletAtvalto
    {
       

        public static double CelsiusToFahrenheit(double celsius)
        {

            return (celsius * 1.8) + 32;
        }

        public static double FahrenheitToCelsius(double fahrenheit)
        {

            return (fahrenheit - 32) / 1.8;
          
        }
    }
}
```

### Az alkalmazás felülete így fog kinézni:

![A felület](honkonvertalo.png)

### A felületet leíró XAML kód (egy Gird-et használunk):
```XML
<Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="3*"/>
            <RowDefinition Height="3*"/>
            <RowDefinition Height="3*"/>
            <RowDefinition Height="2*"/>
        </Grid.RowDefinitions>
        <TextBox x:Name="homersekletErtek" AutomationProperties.AutomationId="homersekletErtek" MaxHeight="30" MaxWidth="100" FontSize="20" />
        <StackPanel Grid.Row="1" HorizontalAlignment="Center" VerticalAlignment="Center">
            <RadioButton x:Name="celsiusKivalaszt" AutomationProperties.AutomationId="celsiusKivalaszt"  Content="Konvertálás celsius-ra" FontSize="16" VerticalContentAlignment="Center" />
            <RadioButton x:Name="fahrenheitKivalaszt" AutomationProperties.AutomationId="fahrenheitKivalaszt"  Content="Konvertálás fahrenheit-re" FontSize="16" VerticalContentAlignment="Center" IsChecked="True" />
        </StackPanel>
        <TextBlock x:Name="konvertaltHomerseklet" AutomationProperties.AutomationId="konvertaltHomerseklet" FontSize="30" HorizontalAlignment="Center" VerticalAlignment="Center" Grid.Row="2" />
        <Button x:Name="buttonKonvertalas" AutomationProperties.AutomationId="buttonKonvertalas" Content="Számolás" Grid.Row="3" MaxWidth="100" MaxHeight="30" Click="buttonKonvertalas_Click" />
    </Grid>
```
Az egyes elemeknél feltűnhet az **AutomationProperties.AutomationId**. Ezt az azonosítót használjuk a tesztben majd az elem kiválasztására, elérésére.

A **Számolás** feliratú gomb lenyomására végezzük el a kívánt műveleteket a mögöttes kódban (**MainWindow.xaml.cs**) :

```C#
private void buttonKonvertalas_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                if (fahrenheitKivalaszt.IsChecked==true)
                {
                    konvertaltHomerseklet.Text = Convert.ToString(HomersekletAtvalto.CelsiusToFahrenheit(Convert.ToDouble(homersekletErtek.Text)));
                } else
                {
                    konvertaltHomerseklet.Text = Convert.ToString(HomersekletAtvalto.FahrenheitToCelsius(Convert.ToDouble(homersekletErtek.Text)));
                }
                

            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message,"Hiba!");
                
            }
            
        }
```
## A tesztelési környezet kiépítése

Az előbb egy meglehetősen egyszerű programot készítettünk a valóságban ez egy jóval összetettebb program lenne... Gondoljuk azt, hogy egy bonyolult programot tesztelünk.

## Appium
Az Appium  nyílt forrású tesztelési környezet, IOS, Windows, Android alkalmazások teszteléséhez.

[Az Appium weboldala](https://appium.io/)

Az automatizáláshoz szükség lesz egy Windows Application Driver nevű alkalmazás telepítésére.

[Windows Application Driver letöltése](https://github.com/microsoft/WinAppDriver/releases/download/v1.1/WindowsApplicationDriver.msi)

A Windows Application Driver működéséhez Win10 alatt engedélyezni kell a **Fejlesztői mód**-ot, különben nem működik! Ez a **Fejlesztői beállítások** rendszerbeállítások alatt található.

A Windows Application Driver-t el kell indítani, ezt követően lehet az (automatizáló) teszteket lefuttatni. 

## Nunit teszt hozzáadása a projekthez

Adjunk hozzá a projekthet egy Nunit tesztet, **HomersekletAtvaltoTeszt** néven. Számos Nuget csomag telepítésére szükség lesz.

Jobb gomb a solution-ön majd **Manage Nuget Packages for Solution**
Először az Nunit-ot keressük meg, telepítsük.

Ezt követően keressük meg és telepítsük az **Appium.Webdriver** csomagot (számos csomagot fog telepíteni).

Kezdődhet a teszt írása.

## A teszt megírása

A teszt forráskódjának a nevét változtassuk meg , legyen mondjuk **AtvaltoAutoTest.cs**. 

A kiinduló állapot:

```C#
using System;
using NUnit.Framework;


namespace HomersekletAtvaltoTeszt
{
    [TestFixture]
    public class AtvaltoAutoTest
    {
        [Test]
        public void TestMethod1()
        {
        
        }
    }
}    
```

Az AtvaltoAutoTest osztályon belül számos változót létre kell hozni, a megfelelő névtereket hozzáadni (CTRL+.)
Hozzáadjuk a szükséges, és egyenlőre üres metódusokat.

```C#
using System;
using NUnit.Framework;
using OpenQA.Selenium.Appium.Windows;
using OpenQA.Selenium.Appium;
using System.Threading;

namespace HomersekletAtvaltoTeszt
{

  [TestFixture]
    public class AtvaltoAutoTest
    {
        protected const string WindowsApplicationDriverUrl = "http://127.0.0.1:4723";
        
        //itt a program futtatható fájljának útvonalát kell beállítani, ami valószínűleg eltér az itt szereplő útvonaltól!!
        private const string WpfProgramId = @"G:\c#_uj_projektek\WpfHomersekletAtvalto\WpfHomersekletAtvalto\bin\Debug\WpfHomersekletAtvalto.exe";
        
        //itt a program bin/debug mappájának útvonalát kell beállítani, ami valószínűleg eltér az itt szereplő útvonaltól!!
        private const string WpfProgramPath = @"G:\c#_uj_projektek\WpfHomersekletAtvalto\WpfHomersekletAtvalto\bin\Debug\
        
        protected static WindowsDriver<WindowsElement> driver;
        
        [OneTimeSetUp]
        public static void Setup()
        {
        
        }
        
        [Test]
        public void CelsiusToFahrenheitTest()
        {
         
        }
        
        [Test]
        public void FahrenheitToCelsiusTest()
        {

        }
        
        [OneTimeTearDown]
        public void EndTest()
        {
                       
        }
        
        
        
    }
```

Több annotáció is szerepel, amit korábban még nem használtunk. 
A [OneTimeSetup] annotáció alatti metódus a kezdeti tevékenységek elvégzésére szolgál, a [OneTimeTearDown] pedig a teszt utáni lezáró tevékenységeket végző metódus elé kerül.


A **Setup()** metódus a következő lesz, néhány beállítás, és a driver létrehozása zajlik itt:

```C#
[OneTimeSetUp]
public static void Setup()
{
    if (driver == null)
            {
                var appiumOptions = new AppiumOptions();
                appiumOptions.AddAdditionalCapability("app", WpfProgramId);
                appiumOptions.AddAdditionalCapability("deviceName", "WindowsPC");
                driver = new WindowsDriver<WindowsElement>(new Uri(WindowsApplicationDriverUrl), appiumOptions);


    }
}
```

Következik az egyik tesztelő metódus:
```C#
[Test]
public void CelsiusToFahrenheitTest()
{
    var homersekletErtek = driver.FindElementByAccessibilityId("homersekletErtek");
    homersekletErtek.SendKeys("1");
    driver.FindElementByAccessibilityId("buttonKonvertalas").Click();

    var konvertaltHomerseklet = driver.FindElementByAccessibilityId("konvertaltHomerseklet");
    
    Assert.AreEqual(Convert.ToDouble(konvertaltHomerseklet.Text), 33.8,0.001);


}
```
Működése: Beazonosítjuk az adatbevitelre szolgáló szövegmezőt, beleírunk a SendKeys paranccsal egy értéket, majd beazonosítjuk a gombot, és rá is kattintunk. 
Beazonosítjuk az eredményt megjelenítő elemet.
Végül az Assert-el megnézzük, hogy az elvárt érték jelenik-e meg az eredménynél.

A másik tesztelő metódus:

```C#
[Test]
public void FahrenheitToCelsiusTest()
{
    var homersekletErtek = driver.FindElementByAccessibilityId("homersekletErtek");
    homersekletErtek.Clear(); 

    homersekletErtek.SendKeys("33,8");

    driver.FindElementByAccessibilityId("celsiusKivalaszt").Click();
    driver.FindElementByAccessibilityId("buttonKonvertalas").Click();

    var konvertaltHomerseklet = driver.FindElementByAccessibilityId("konvertaltHomerseklet");          
    
    Assert.AreEqual(Convert.ToDouble(konvertaltHomerseklet.Text), 1,0.001);
}
```
Ebben a metódusban annyi különbség van, hogy a **homersekletErtek.Clear()** paranccsal töröljük a beviteli mező tartalmát.

Végül a tesztet lezátó metódus:

```C#
[OneTimeTearDown]
public void EndTest()
{

    if (driver != null)
    {
        driver.Close();
        driver.Quit();
    }

}
```
## Riport készítése a tesztek eredményeiről

A Test Explorer ablakában láthatja a tesztelő a tesztek eredményét, azonban nem biztos, hogy ez elegendő. Lehet hogy a projektmanager is kíváncsi lenne ezekre az információkra. A megoldás, riport készítése a tesztek futásának eredményéről. Ennek a feladatnak a megoldására számos eszköz létezik, ezek közül megnézzük az Extent Report-ot.

Mint annyi minden más, ez is Nuget csomagban megtalálható, tehát fel kell telepíteni a teszt projektünkhöz.

**Manage Nuget Packages for Solution - > ExtentReports** 

Számos más csomagot is telepíteni fog.

A teszt osztályunk változóihoz adjuk hozzá a következőket:

```C#
 protected static ExtentReports extReport;
 protected static ExtentTest extTest;
```

Aláhúzza pirossal, CTRL+. -al töltsük be a szükséges névteret. A riportnak is szüksége van kezdeti beállításokra, ezeket egy 
[OneTimeSetup] annotációval megjelölt metódusba tesszük. A metódus a **Setup()** alá kerüljön (de mindenféleképp a tesztek elé)!

A metódus a következő (ebben az esetben a riport a bin/debug mappába kerül, de mehet máshová is!):

```C#
 [OneTimeSetUp]
 public static void ReportSetup()
 {
    extReport = new ExtentReports();
    var htmlReporter = new ExtentHtmlReporter(@"G:\c#_uj_projektek\WpfHomersekletAtvalto\WpfHomersekletAtvalto\bin\Debug\testresults.html");
    extReport.AddSystemInfo("Hőmérséklet átváltás teszt", "Auto teszt");
    extReport.AddSystemInfo("Tesztelő:", "KR");
    extReport.AttachReporter(htmlReporter);
    htmlReporter.Config.DocumentTitle = "HTML teszt riport";
    htmlReporter.Config.ReportName = "Hőmérséklet átváltás teszt";
    htmlReporter.Config.Theme = AventStack.ExtentReports.Reporter.Configuration.Theme.Standard;

 }
```

Innentől kezdve, a teszt egy általunk kiválasztott pontján kedvünkre loggolhatunk:

A **CelsiusToFahrenheitTest()** metódusba illesszük be az első sorba rögtön:
```C#
 extTest = extReport.CreateTest("Celsius to Fahrenheit teszt");
```

A **CelsiusToFahrenheitTest()** metódusba illesszük be az **Assert** sor után:

```C#
extTest.Log(Status.Pass, "Celsius to Fahrenheit teszt OK");
```
A **FahrenheitToCelsiusTest()** metódusba pedig illesszük be az első sorba rögtön:

```C#
extTest = extReport.CreateTest("Fahrenheit to Celsius teszt");
```

A **FahrenheitToCelsiusTest()** metódusba pedig illesszük be az **Assert** sor után:

```C#
extTest.Log(Status.Pass, "Fahrenheit to Celsius teszt OK");
```
Az EndTest() metódusba tegyük bele:
```C#
 extReport.Flush();
```

Ha ezek után lefuttatjuk a tesztet, akkor a megadott mappában létre fog jönni egy **index.html** és egy **dashboard.html** fájl a tesztek eredményével.

Ez az alap megoldás. Látható, hogy akkor remekül működik, amikor a tesztek hibátlanul lefutnak. De vajon mi a helyzet, ha valami hiba történik? A hibás tesztek kezelésére egy külön metódust írunk. Ezt a metódust [TearDown] annotációval fogjuk jelölni, ez minden egyes teszt után le fog futni. A metódust az utolsó tesztmetódus után helyezzük el.

A metódus:

```C#
 [TearDown]
public static void CloseReport()
{
    var status = TestContext.CurrentContext.Result.Outcome.Status;         
    var stacktrace = TestContext.CurrentContext.Result.StackTrace;
    var errormsg = TestContext.CurrentContext.Result.Message;

            if (status == TestStatus.Failed)
            {
                ITakesScreenshot shot = (ITakesScreenshot)driver;

                Screenshot screenshot = shot.GetScreenshot();
                screenshot.SaveAsFile(WpfProgramPath + "ErrScreen.png", ScreenshotImageFormat.Png);
                extTest.Log(Status.Fail, stacktrace + errormsg);
                extTest.Log(Status.Fail, "Képernyő:");
                extTest.AddScreenCaptureFromPath("ErrScreen.png");
            }
            
}
```
Amennyiben a tesztünk hibára fut akkor a CloseReport() metódus rögzíti a StackTrace értékét, a hibaüzenetet, illetve egy képenyőmentést is készít a program ablakról, ami a riportban a kép ikonjára kattintva megtekinthető. 

Könnyen belátható, hogy sokkal könnyebb így futtatni pl. 2 tesztet 100-100 teszt esettel, és ezek eredményét utólag kiértékelni, mint ezeket kézzel elvégezni.

### Tesztmetódus több teszt esettel

Egy teszt nem teszt, egy metódus tesztelését célszerű több értékkel elvégezni. A teszt metódusnál ebben az esetben használjuk a **[TestCase]** annotációt.
pl. [TestCase(1,33.8)] -> Az első érték lesz a bemenet, a második pedig az eredményként várt érték. A [TestCase] annotációból akármennyit tehetünk a teszt metódushoz. 

**A módosított teszt metódus**

```c#    
[Test]
[TestCase(33, 91.40)]
[TestCase(21, 69.80)]
[TestCase(12, 53.60)]
//Hibás
[TestCase(12, 55.60)]
[TestCase(1,33.8)]
//Hibás
[TestCase(1, 34.8)]
 public void CelsiusToFahrenheitTest(double celsius,double result)
{
    extTest = extReport.CreateTest("Celsius to Fahrenheit teszt");
    var homersekletErtek = driver.FindElementByAccessibilityId("homersekletErtek");
    homersekletErtek.Clear();
    homersekletErtek.SendKeys(Convert.ToString(celsius));
    driver.FindElementByAccessibilityId("buttonKonvertalas").Click();

    var konvertaltHomerseklet = driver.FindElementByAccessibilityId("konvertaltHomerseklet");
    
    Assert.AreEqual(Convert.ToDouble(konvertaltHomerseklet.Text), result,0.001);
    extTest.Log(Status.Pass, "Celsius to Fahrenheit teszt OK");

}
```
A metódus négyszer fog lefutni. Ügyelni kell arra, hogy a teszt előtt a beviteli mező tartalmát töröljük, különben hibásan fog működni a tesztünk. Ezért kell a **homersekletErtek.Clear()** parancs pluszban.

**Módosítsuk a másik metódust is!**

```C#
[Test]
[TestCase(33.8,1)]
//Hibás
[TestCase(33.8, 2)]
[TestCase(91.40,33)]
[TestCase(69.80,21)]
[TestCase(53.60,12)]
public void FahrenheitToCelsiusTest(double fahrenheit,double result)
{
    extTest = extReport.CreateTest("Fahrenheit to Celsius teszt");
    var homersekletErtek = driver.FindElementByAccessibilityId("homersekletErtek");
    homersekletErtek.Clear();

    homersekletErtek.SendKeys(Convert.ToString(fahrenheit));

    driver.FindElementByAccessibilityId("celsiusKivalaszt").Click();
    driver.FindElementByAccessibilityId("buttonKonvertalas").Click();

    var konvertaltHomerseklet = driver.FindElementByAccessibilityId("konvertaltHomerseklet");
                       
    Assert.AreEqual(Convert.ToDouble(konvertaltHomerseklet.Text), result,0.001 );
    extTest.Log(Status.Pass, "Fahrenheit to Celsius teszt OK");
}
```

Látszik, hogy mindkét metódushoz tettem olyan teszteseteket is, amelyek hibára fognak futni a nem megfelelő értékek miatt.
Viszont így már a CloseReport() metódusnál a képernyőmentés nem leszt jó, hiszen fixen beledrótoztuk első körben a fájlnevet. 

Javítsuk ezt ki!

Valami olyan információ kellene, amely egyértelműen beazonosítja a tesztesetet. A tesztbe beküldött paramétereket fel lehet erre a célra használni.

Nézzük a CloseReport() metódust! Azon tevékenységek közé, amelyek hiba esetén futnak le, tegyük bele a következőket:

```C#
var be = TestContext.CurrentContext.Test.Arguments.GetValue(0);
var elvart = TestContext.CurrentContext.Test.Arguments.GetValue(1);
string filename = be + "_" + elvart;
```
A **be** és az **elvart** változókba le tudjuk kérni a teszt argumentumait, és ebből építünk fájlnevet.

Módosul még pár sor:
```C#
screenshot.SaveAsFile(WpfProgramPath +filename+".png", ScreenshotImageFormat.Png);
extTest.AddScreenCaptureFromPath(filename + ".png");
```
Mostmár nem lesz felülírás, minden hibás tesztnél meg lehet nézni a mentett képernyőt.

Az egész **CloseReport()** metódus egyben:
```C#
[TearDown]
public static void CloseReport()
{
    var status = TestContext.CurrentContext.Result.Outcome.Status;        
    var stacktrace = TestContext.CurrentContext.Result.StackTrace;
    var errormsg = TestContext.CurrentContext.Result.Message;

            if (status == TestStatus.Failed)
            {
                ITakesScreenshot shot = (ITakesScreenshot)driver;
                var be = TestContext.CurrentContext.Test.Arguments.GetValue(0);
                var elvart = TestContext.CurrentContext.Test.Arguments.GetValue(1);

                string filename = be + "_" + elvart;
                Debug.WriteLine(filename);
                Screenshot screenshot = shot.GetScreenshot();
                screenshot.SaveAsFile(WpfProgramPath +filename+".png", ScreenshotImageFormat.Png);
                extTest.Log(Status.Fail, stacktrace + errormsg);
                extTest.Log(Status.Fail, "Képernyő:");
                extTest.AddScreenCaptureFromPath(filename + ".png");
            }

}
```
