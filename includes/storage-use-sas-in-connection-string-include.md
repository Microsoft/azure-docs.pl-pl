---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183449"
---
Jeśli masz adres URL sygnatury dostępu współdzielonego (SAS), który umożliwia dostęp do zasobów na koncie magazynu, możesz użyć SAS w parametrach połączenia. Ze względu na to, że sygnatura dostępu współdzielonego zawiera informacje wymagane do uwierzytelnienia żądania, parametry połączenia z sygnaturą dostępu współdzielonego udostępniają protokół, punkt końcowy usługi oraz poświadczenia niezbędne do uzyskania dostępu do zasobu.

Aby utworzyć parametry połączenia, które zawierają sygnaturę dostępu współdzielonego, Określ ciąg w następującym formacie:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Każdy punkt końcowy usługi jest opcjonalny, chociaż parametry połączenia muszą zawierać co najmniej jedną.

> [!NOTE]
> Najlepszym rozwiązaniem jest użycie protokołu HTTPS z sygnaturą dostępu współdzielonego.
>
> Jeśli określisz sygnaturę dostępu współdzielonego w parametrach połączenia w pliku konfiguracji, może być konieczne zakodowanie znaków specjalnych w adresie URL.
>
>

### <a name="service-sas-example"></a>Przykład sygnatury dostępu współdzielonego usługi
Oto przykład parametrów połączenia, które obejmują sygnaturę dostępu współdzielonego usługi dla magazynu obiektów blob:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

Oto przykład tego samego ciągu połączenia z kodowaniem znaków specjalnych:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Przykład SAS konta
Oto przykład parametrów połączenia, które obejmują sygnaturę dostępu współdzielonego konta dla obiektów blob i magazynu plików. Należy pamiętać, że punkty końcowe dla obu usług są określone:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

Oto przykład tych samych parametrów połączenia z kodowaniem adresów URL:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

