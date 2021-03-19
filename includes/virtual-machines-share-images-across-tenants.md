---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "73903702"
---
Jeśli jednak chcesz udostępniać obrazy poza dzierżawcą platformy Azure, należy utworzyć rejestrację aplikacji w celu ułatwienia udostępniania.  Korzystanie z rejestracji aplikacji może umożliwić bardziej złożone scenariusze udostępniania, takie jak: 

* Zarządzanie obrazami udostępnionymi, gdy jedna firma uzyska inną, a infrastruktura platformy Azure jest rozłożona na poszczególne dzierżawy. 
* Partnerzy platformy Azure zarządzają infrastrukturą platformy Azure w imieniu swoich klientów. Dostosowanie obrazów odbywa się w ramach dzierżawy partnerów, ale wdrożenia infrastruktury będą wykonywane w dzierżawie klienta. 


## <a name="create-the-app-registration"></a>Utwórz rejestrację aplikacji

Utwórz rejestrację aplikacji, która będzie używana przez obie dzierżawy do udostępniania zasobów galerii obrazów.
1. Otwórz [rejestracje aplikacji (wersja zapoznawcza) w Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Wybierz pozycję **Nowa rejestracja** w menu w górnej części strony.
1. W polu **Nazwa** wpisz *myGalleryApp*.
1. W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft**.
1. W polu **Identyfikator URI przekierowania** wpisz, *https://www.microsoft.com* a następnie wybierz pozycję **zarejestruj**. Po utworzeniu rejestracji aplikacji zostanie otwarta strona przegląd.
1. Na stronie Przegląd Skopiuj **Identyfikator aplikacji (klienta)** i Zapisz go do użycia później.   
1. Wybierz pozycję **certyfikaty & wpisy tajne**, a następnie wybierz pozycję **nowy klucz tajny klienta**.
1. W polu **Opis** wpisz wpis *tajny galerii obrazów udostępnionych dla wielu dzierżawców*.
1. W obszarze **wygaśnięcie** pozostaw wartość domyślną **w 1 roku** , a następnie wybierz pozycję **Dodaj**.
1. Skopiuj wartość wpisu tajnego i Zapisz ją w bezpiecznym miejscu. Nie można go pobrać po opuszczeniu strony.


Nadaj uprawnienia do rejestracji aplikacji, aby korzystać z galerii obrazów udostępnionych.
1. W Azure Portal Wybierz galerię udostępnionych obrazów, która ma być udostępniana innym dzierżawcom.
1. Wybierz pozycję **Wybierz kontrolę dostępu (IAM)** i w obszarze **Dodaj przypisanie roli** wybierz pozycję *Dodaj*. 
1. W obszarze **rola** wybierz pozycję **czytelnik**.
1. W obszarze **Przypisz dostęp do:** pozostaw to pole **użytkownik, Grupa lub nazwa główna usługi Azure AD**.
1. W obszarze **Wybierz** wpisz *myGalleryApp* i wybierz go, gdy zostanie wyświetlony na liście. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.


## <a name="give-tenant-2-access"></a>Udzielanie dostępu do dzierżawy 2

Przyznaj dzierżawcom 2 dostęp do aplikacji, żądając logowania przy użyciu przeglądarki. Zamień na *\<Tenant2 ID>* Identyfikator dzierżawy dla dzierżawy, z którą chcesz udostępnić galerię obrazów. Zamień na *\<Application (client) ID>* Identyfikator aplikacji utworzonej przez Ciebie rejestracji aplikacji. Po dokonaniu zamiany, wklej adres URL do przeglądarki i postępuj zgodnie z monitami logowania, aby zalogować się do dzierżawy 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

W [Azure Portal](https://portal.azure.com) Zaloguj się jako dzierżawca 2 i nadajesz rejestracji aplikacji dostęp do grupy zasobów, w której chcesz utworzyć maszynę wirtualną.

1. Wybierz grupę zasobów, a następnie wybierz pozycję **Kontrola dostępu (IAM)**. W obszarze **Dodaj przypisanie roli** wybierz pozycję **Dodaj**. 
1. W obszarze **rola** wpisz **współautor**.
1. W obszarze **Przypisz dostęp do:** pozostaw to pole **użytkownik, Grupa lub nazwa główna usługi Azure AD**.
1. W obszarze **Wybierz** typ *myGalleryApp* wybierz ją, gdy zostanie ona wyświetlona na liście. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

> [!NOTE]
> Musisz poczekać na zakończenie kompilowania i replikowania wersji obrazu, aby można było użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu.

