---
title: Jak używać magazynu obiektów (BLOB) z platformy Xamarin | Microsoft Docs
description: Biblioteka klienta usługi Azure Storage dla środowiska Xamarin umożliwia deweloperom tworzenie aplikacji w sklepie dla systemów iOS, Android i Windows przy użyciu natywnych interfejsów użytkownika. W tym samouczku pokazano, jak utworzyć aplikację korzystającą z usługi Azure Blob Storage za pomocą platformy Xamarin.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: a724cf309ef0677b79636f6789de9a6c8f9ba248
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465511"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Jak używać Blob Storage z platformy Xamarin

Platforma Xamarin umożliwia deweloperom użycie udostępnionej bazy kodu w języku C# do tworzenia aplikacji w sklepie dla systemów iOS, Android i Windows przy użyciu natywnych interfejsów użytkownika. W tym samouczku pokazano, jak używać usługi Azure Blob Storage z aplikacją platformy Xamarin. Jeśli chcesz dowiedzieć się więcej na temat usługi Azure Storage, przed wprowadzeniem do kodu, zobacz [wprowadzenie do Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Tworzenie nowej aplikacji platformy Xamarin

W tym samouczku utworzysz aplikację, która jest przeznaczona dla systemów Android, iOS i Windows. Ta aplikacja spowoduje po prostu utworzenie kontenera i przekazanie obiektu BLOB do tego kontenera. Będziemy korzystać z programu Visual Studio w systemie Windows, ale te same informacje mogą być stosowane podczas tworzenia aplikacji przy użyciu Xamarin Studio w macOS.

Wykonaj następujące kroki, aby utworzyć aplikację:

1. Jeśli jeszcze tego nie zrobiono, Pobierz i zainstaluj platformę [Xamarin dla programu Visual Studio](https://www.xamarin.com/download).
2. Otwórz program Visual Studio i Utwórz pustą aplikację (natywną przenośną): **File > New > Project > dla wielu Platform > pusta aplikacja (natywna przenośna)**.
3. W okienku Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**. Wyszukaj **windowsazure. Storage** i zainstaluj najnowszą stabilną wersję dla wszystkich projektów w rozwiązaniu.
4. Skompiluj i Uruchom projekt.

Teraz powinna być dostępna aplikacja, która pozwala na kliknięcie przycisku, który zwiększa licznik.

## <a name="create-container-and-upload-blob"></a>Tworzenie kontenera i przekazywanie obiektu BLOB

Następnie w obszarze `(Portable)` projektu dodasz kod do `MyClass.cs` . Ten kod tworzy kontener i przekazuje obiekt BLOB do tego kontenera. `MyClass.cs`powinien wyglądać następująco:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Pamiętaj, aby zastąpić ciąg "your_account_name_here" i "your_account_key_here" rzeczywistą nazwą konta i kluczem konta.

Wszystkie projekty dla systemów iOS, Android i Windows Phone zawierają odwołania do Twojego przenośnego projektu — co oznacza, że można napisać cały współużytkowany kod w jednym miejscu i używać go we wszystkich projektach. Teraz możesz dodać następujący wiersz kodu do każdego projektu, aby rozpocząć korzystanie z zalet:`MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp. Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp. iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp. WinPhone > MainPage. XAML > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Teraz możesz uruchomić tę aplikację w emulatorze systemu Android lub Windows Phone. Możesz również uruchomić tę aplikację w emulatorze systemu iOS, ale będzie to wymagało komputera Mac. Szczegółowe instrukcje na ten temat można znaleźć w dokumentacji dotyczącej [łączenia programu Visual Studio z komputerem Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Po uruchomieniu aplikacji zostanie utworzony kontener `mycontainer` na koncie magazynu. Powinien zawierać obiekt BLOB, `myblob` który zawiera tekst, `Hello, world!` . Można to sprawdzić przy użyciu [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/).

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak utworzyć międzyplatformową aplikację w programie Xamarin, która korzysta z usługi Azure Storage, a w każdym scenariuszu w Blob Storage. Można jednak wykonać wiele więcej, nie tylko Blob Storage, ale również z tabelą, plikiem i Queue Storage. Aby dowiedzieć się więcej, zapoznaj się z następującymi artykułami:

* [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](storage-dotnet-how-to-use-blobs.md)
* [Wprowadzenie do usługi Azure Files](../files/storage-files-introduction.md)
* [Tworzenie oprogramowania dla usługi Azure Files przy użyciu platformy .NET](../files/storage-dotnet-how-to-use-files.md)
* [Rozpoczynanie pracy z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Rozpoczynanie pracy z usługą Azure queue storage przy użyciu platformy .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
