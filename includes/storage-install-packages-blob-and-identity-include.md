---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74806590"
---
## <a name="install-client-library-packages"></a>Zainstaluj pakiety biblioteki klienta

> [!NOTE]
> W przykładach pokazano, jak używać biblioteki klienckiej usługi Azure Storage w wersji 12. Biblioteka klienta w wersji 12 jest częścią zestawu Azure SDK. Aby uzyskać więcej informacji na temat zestawu Azure SDK, zobacz repozytorium zestawu Azure SDK w witrynie [GitHub](https://github.com/Azure/azure-sdk).

Aby zainstalować pakiet usługi BLOB Storage, uruchom następujące polecenie w konsoli Menedżera pakietów NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

W przykładach pokazano również, jak używać najnowszej wersji [biblioteki klienta tożsamości platformy Azure dla platformy .NET](https://www.nuget.org/packages/Azure.Identity/) do uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Aby zainstalować pakiet, uruchom następujące polecenie w konsoli Menedżera pakietów NuGet:

```powershell
Install-Package Azure.Identity
```
