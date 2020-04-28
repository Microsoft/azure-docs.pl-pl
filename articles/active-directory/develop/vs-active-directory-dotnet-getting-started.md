---
title: Rozpoczynanie pracy z usługą Azure AD w projektach platformy .NET MVC | Azure
description: Jak rozpocząć korzystanie z Azure Active Directory w projektach .NET MVC po nawiązaniu połączenia z usługą Azure AD lub utworzeniu jej przy użyciu usług połączonych programu Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: fe408e965c528db1d82b73ee7b20bbe3b3933657
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886130"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Wprowadzenie z Azure Active Directory (projekty ASP.NET MVC)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-dotnet-getting-started.md)
> - [Co się stało](vs-active-directory-dotnet-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki po dodaniu Active Directory do projektu MVC ASP.NET za pomocą polecenia **project > Connect Services** programu Visual Studio. Jeśli usługa nie została jeszcze dodana do projektu, możesz to zrobić w dowolnym momencie.

Zobacz, [co się stało z moim projektem MVC?](vs-active-directory-dotnet-what-happened.md) dla zmian wprowadzonych w projekcie podczas dodawania połączonej usługi.

## <a name="requiring-authentication-to-access-controllers"></a>Wymaganie uwierzytelniania do kontrolerów dostępu

Wszystkie kontrolery w projekcie zostały wbudowane przy użyciu `[Authorize]` atrybutu. Ten atrybut wymaga uwierzytelnienia użytkownika przed uzyskaniem dostępu do tych kontrolerów. Aby umożliwić dostęp do kontrolera anonimowo, Usuń ten atrybut z kontrolera. Jeśli chcesz ustawić uprawnienia na bardziej szczegółowym poziomie, zastosuj atrybut do każdej metody wymagającej autoryzacji zamiast zastosowania jej do klasy kontrolera.

## <a name="adding-signin--signout-controls"></a>Dodawanie kontrolek Signing/wylogowaniu

Aby dodać kontrolki logowania/wylogowaniu do widoku, możesz użyć widoku `_LoginPartial.cshtml` częściowego, aby dodać funkcje do jednego z widoków. Oto przykład funkcji dodanych do widoku standardowego `_Layout.cshtml` . (Należy zwrócić uwagę na ostatni element w bloku DIV z klasą nawigacyjną — zwijanie):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla Azure Active Directory](authentication-scenarios.md)
- [dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET](quickstart-v2-aspnet-webapp.md)
