---
title: 'Instrukcje: przywracanie lub usuwanie ostatnio usuniętej aplikacji za pomocą platformy tożsamości firmy Microsoft | Azure'
titleSuffix: Microsoft identity platform
description: W tym instruktażu dowiesz się, jak przywrócić lub trwale usunąć ostatnio usuniętą aplikację zarejestrowaną na platformie tożsamości firmy Microsoft.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: dbe3e16bf56c5480fbe4aff8dad78bbbb5591f67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080340"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Przywracanie lub usuwanie ostatnio usuniętej aplikacji za pomocą platformy tożsamości firmy Microsoft
Po usunięciu rejestracji aplikacji Aplikacja pozostaje w stanie wstrzymania przez 30 dni. W tym 30-dniowym oknie Rejestracja aplikacji może być przywracana wraz ze wszystkimi jej właściwościami. Po upływie tego 30-dniowego okna rejestracje aplikacji nie mogą zostać przywrócone i trwały proces usuwania może być automatycznie uruchamiany.  Ta funkcja ma zastosowanie tylko do aplikacji skojarzonych z katalogiem.  Nie jest dostępna dla aplikacji z konto Microsoft osobistych, których nie można przywrócić.

Możesz wyświetlić usunięte aplikacje, przywrócić usuniętą aplikację lub trwale usunąć aplikację przy użyciu środowiska Rejestracje aplikacji w obszarze Azure Active Directory (Azure AD) w Azure Portal.

Należy pamiętać, że nie może zostać przywrócona trwale usunięta aplikacja ani pomoc techniczna firmy Microsoft.

> [!IMPORTANT]
> Funkcja interfejsu użytkownika portalu usuniętych aplikacji [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby trwale usunąć aplikacje, musisz mieć jedną z następujących ról.

- Administrator globalny
- Administrator aplikacji
- Administrator aplikacji w chmurze
- Administrator tożsamości hybrydowej
- Właściciel aplikacji

Aby przywrócić aplikacje, musisz mieć jedną z następujących ról.

- Administrator globalny
- Właściciel aplikacji

### <a name="view-your-deleted-applications"></a>Wyświetlanie usuniętych aplikacji
Wszystkie aplikacje są widoczne w stanie nietrwałego usunięcia.  Można przywrócić tylko te aplikacje, które zostały usunięte krócej niż 30 dni temu.

#### <a name="to-view-your-restorable-applications"></a>Aby wyświetlić aplikacje dostępnych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wyszukaj i wybierz pozycję **Azure Active Directory**, wybierz pozycję **rejestracje aplikacji**, a następnie wybierz kartę **usunięte aplikacje (wersja zapoznawcza)** .

Przejrzyj listę aplikacji. Tylko aplikacje, które zostały usunięte w ciągu ostatnich 30 dni, są dostępne do przywrócenia. W przypadku korzystania z wersji zapoznawczej wyszukiwania Rejestracje aplikacji można filtrować według kolumny "Data usunięcia", aby wyświetlić tylko te aplikacje.

## <a name="restore-a-recently-deleted-application"></a>Przywróć ostatnio usuniętą aplikację

Po usunięciu rejestracji aplikacji z organizacji aplikacja jest w stanie wstrzymania, a jej konfiguracje są zachowywane. Podczas przywracania rejestracji aplikacji, jej konfiguracje również są przywracane.  Jeśli jednak w **aplikacjach dla przedsiębiorstw** dla dzierżawy głównej aplikacji wystąpiły jakieś ustawienia specyficzne dla organizacji, nie zostaną one przywrócone.  

Wynika to z faktu, że ustawienia specyficzne dla organizacji są przechowywane w oddzielnym obiekcie nazywanym nazwą główną usługi.  Ustawienia przechowywane w jednostce usługi obejmują uprawnienia i przypisania użytkowników i grup dla określonej organizacji; te konfiguracje nie zostaną przywrócone po przywróceniu aplikacji. Aby uzyskać więcej informacji, zobacz [obiekty główne aplikacji i usługi](app-objects-and-service-principals.md). 


### <a name="to-restore-an-application"></a>Aby przywrócić aplikację
1. Na karcie **usunięte aplikacje (wersja zapoznawcza)** Wyszukaj i wybierz jedną z aplikacji usuniętych poniżej 30 dni temu.
2. Wybierz pozycję **Przywróć rejestrację aplikacji**.

## <a name="permanently-delete-an-application"></a>Trwałe usuwanie aplikacji
Możesz ręcznie usunąć aplikację z organizacji. Trwale usunięta aplikacja nie może zostać przywrócona przez użytkownika, innego administratora lub przez dział pomocy technicznej firmy Microsoft.

### <a name="to-permanently-delete-an-application"></a>Aby trwale usunąć aplikację

1. Na karcie **usunięte aplikacje (wersja zapoznawcza)** Wyszukaj i wybierz jedną z dostępnych aplikacji.
2. Wybierz pozycję **Usuń trwale**.
3. Przeczytaj tekst ostrzegawczy i wybierz opcję **tak**.

## <a name="next-steps"></a>Następne kroki
Po przywróceniu lub usunięciu trwałej aplikacji możesz:

- [Dodaj aplikację](quickstart-register-app.md).
- Dowiedz się więcej o [obiektach głównych aplikacji i usług](app-objects-and-service-principals.md) na platformie tożsamości firmy Microsoft.
