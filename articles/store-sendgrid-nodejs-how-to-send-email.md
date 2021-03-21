---
title: Jak korzystać z usługi poczty e-mail SendGrid (Node.js) | Microsoft Docs
description: Dowiedz się, jak wysyłać wiadomości e-mail za pomocą usługi poczty e-mail SendGrid na platformie Azure. Przykłady kodu zapisywane przy użyciu interfejsu API Node.js.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015432"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Jak wysyłać wiadomości E-mail przy użyciu usługi SendGrid z Node.js

W tym przewodniku pokazano, jak wykonywać typowe zadania programistyczne przy użyciu usługi poczty e-mail SendGrid na platformie Azure. Przykłady są zapisywane przy użyciu interfejsu API Node.js. Omówione scenariusze obejmują **konstruowanie poczty e-mail**, **wysyłanie wiadomości e-mail**, **Dodawanie załączników**, **Używanie filtrów** i **Aktualizowanie właściwości**. Aby uzyskać więcej informacji na temat SendGrid i wysyłania wiadomości e-mail, zobacz sekcję [następne kroki](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Co to jest usługa poczty E-mail SendGrid?

SendGrid to [oparta na chmurze usługa poczty e-mail] , która zapewnia niezawodne [dostarczanie transakcyjnych wiadomości e-mail], skalowalność i analizę w czasie rzeczywistym oraz elastyczne interfejsy API, które ułatwiają integrację niestandardową. Typowe scenariusze użycia SendGrid obejmują:

* Automatyczne wysyłanie pokwitowań do klientów
* Administrowanie listami dystrybucyjnymi na potrzeby wysyłania klientów do comiesięcznych ofert e-Fliers i specjalnych
* Zbieranie metryk w czasie rzeczywistym dla elementów, takich jak zablokowane wiadomości e-mail i czas odpowiedzi klienta
* Generowanie raportów ułatwiających identyfikowanie trendów
* Przekazywanie zapytań klienta
* Powiadomienia e-mail z aplikacji

Aby uzyskać więcej informacji, zobacz [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Utwórz konto SendGrid

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Odwoływanie się do modułu Node.js SendGrid

Moduł SendGrid dla Node.js można zainstalować za pomocą Menedżera pakietów węzła (npm) przy użyciu następującego polecenia:

```bash
npm install sendgrid
```

Po zakończeniu instalacji można wymagać modułu w aplikacji przy użyciu następującego kodu:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

Moduł SendGrid eksportuje funkcje **SendGrid** i **poczty e-mail** .
**SendGrid** jest odpowiedzialna za wysyłanie wiadomości e-mail za pośrednictwem internetowego interfejsu API, podczas gdy **poczta e-mail** hermetyzuje wiadomość e-mail.

## <a name="how-to-create-an-email"></a>Instrukcje: tworzenie wiadomości E-mail

Tworzenie wiadomości e-mail przy użyciu modułu SendGrid polega na utworzeniu najpierw wiadomości e-mail za pomocą funkcji poczty e-mail, a następnie wysłaniu jej przy użyciu funkcji SendGrid. Poniżej przedstawiono przykład tworzenia nowej wiadomości za pomocą funkcji poczty E-mail:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Możesz również określić wiadomość HTML dla klientów, którzy obsługują tę aplikację, ustawiając właściwość html. Na przykład:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Ustawienie właściwości text i HTML umożliwia bezpieczne powrót do zawartości tekstowej dla klientów, którzy nie mogą obsługiwać wiadomości w formacie HTML.

Aby uzyskać więcej informacji na temat wszystkich właściwości obsługiwanych przez funkcję poczty E-mail, zobacz [SendGrid-NodeJS][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Instrukcje: wysyłanie wiadomości E-mail

Po utworzeniu wiadomości e-mail za pomocą funkcji poczty E-mail można wysłać ją przy użyciu internetowego interfejsu API dostarczonego przez SendGrid. 

### <a name="web-api"></a>Interfejs API sieci Web

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Chociaż powyższe przykłady pokazują przekazywanie w obiekcie poczty e-mail i funkcji wywołania zwrotnego, można również bezpośrednio wywołać funkcję Send, bezpośrednio określając właściwości wiadomości e-mail. Na przykład:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Instrukcje: Dodawanie załącznika
Załączniki można dodać do wiadomości, określając nazwy plików i ścieżki we właściwości **Files** . Poniższy przykład ilustruje wysyłanie załącznika:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> W przypadku używania właściwości **Files** plik musi być dostępny za pośrednictwem [FS. readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Jeśli plik, który chcesz dołączyć, jest hostowany w usłudze Azure Storage, na przykład w kontenerze obiektów blob, należy najpierw skopiować plik do magazynu lokalnego lub do dysku platformy Azure, zanim będzie można go wysłać jako załącznik przy użyciu właściwości **Files** .
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Instrukcje: Używanie filtrów do włączania stopek i śledzenia

Program SendGrid oferuje dodatkowe funkcje poczty e-mail za pośrednictwem filtrów. Są to ustawienia, które można dodać do wiadomości e-mail w celu włączenia określonych funkcji, takich jak włączenie śledzenia, Google Analytics, śledzenie subskrypcji itd. Aby uzyskać pełną listę filtrów, zobacz [Ustawienia filtru][Filter Settings].

Filtry można zastosować do wiadomości za pomocą właściwości **filters** .
Każdy filtr jest określony przez skrót zawierający ustawienia specyficzne dla filtru.
W poniższych przykładach zademonstrowano stopkę i klikasz filtry śledzenia:

### <a name="footer"></a>Stopka

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Kliknij pozycję Śledzenie

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>Instrukcje: aktualizowanie właściwości poczty E-mail

Niektóre właściwości wiadomości e-mail można zastąpić za pomocą polecenia **SetProperty** lub dołączane za pomocą polecenia **AddProperty**. Na przykład możesz dodać dodatkowych odbiorców za pomocą

```javascript
email.addTo('jeff@contoso.com');
```

lub ustaw Filtr za pomocą

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Aby uzyskać więcej informacji, zobacz [SendGrid-NodeJS][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Instrukcje: korzystanie z dodatkowych usług SendGrid Services

Usługa SendGrid oferuje interfejsy API oparte na sieci Web, których można użyć do korzystania z dodatkowych funkcji SendGrid z aplikacji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [dokumentację interfejsu API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy usługi poczty E-mail SendGrid, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Repozytorium modułów Node.js SendGrid: [SendGrid-NodeJS][sendgrid-nodejs]
* Dokumentacja interfejsu API SendGrid: <https://sendgrid.com/docs>
* Oferta Specjalna SendGrid dla klientów platformy Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[usługa poczty e-mail oparta na chmurze]: https://sendgrid.com/email-solutions
[dostarczanie transakcyjnych wiadomości e-mail]: https://sendgrid.com/transactional-email
