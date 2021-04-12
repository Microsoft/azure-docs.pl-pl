---
title: Dodawanie aplikacji usługi Azure AD i zarządzanie nimi — Azure Marketplace
description: Dowiedz się, jak dodawać aplikacje usługi Azure AD dla komercyjnego programu Marketplace w centrum partnerskim i zarządzać nimi.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108484"
---
# <a name="add-and-manage-azure-ad-applications"></a>Dodawanie aplikacji usługi Azure AD i zarządzanie nimi

**Odpowiednie role**

- Właściciel
- Menedżer

Aplikacje lub usługi, które są częścią Azure Active Directory firmy (Azure AD), można zezwolić na dostęp do konta Centrum partnerskiego.

## <a name="add-existing-azure-ad-applications"></a>Dodawanie istniejących aplikacji usługi Azure AD

Aby dodać aplikacje, które już istnieją w Azure Active Directory firmy:

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj aplikacje usługi Azure AD**.
1. Wybierz co najmniej jedną aplikację usługi Azure AD z wyświetlonej listy. Możesz użyć pola wyszukiwania, aby wyszukać określone aplikacje usługi Azure AD. W przypadku wybrania więcej niż jednej aplikacji usługi Azure AD, która ma zostać dodana do konta Centrum partnerskiego, należy przypisać im tę samą rolę lub zestaw uprawnień niestandardowych. Aby dodać wiele aplikacji usługi Azure AD z różnymi rolami/uprawnieniami, Powtórz te kroki dla każdej roli lub zestawu uprawnień niestandardowych.
1. Po zakończeniu wybierania aplikacji usługi Azure AD wybierz pozycję **Dodaj wybrane**.
1. W sekcji **role** Określ role lub dostosowane uprawnienia dla wybranych aplikacji usługi Azure AD.
1. Wybierz pozycję **Zapisz**.

## <a name="add-new-azure-ad-applications"></a>Dodawanie nowych aplikacji usługi Azure AD

Jeśli chcesz udzielić Centrum partnerskiego dostępu do nowego konta aplikacji usługi Azure AD, możesz je utworzyć w sekcji **Użytkownicy** . Spowoduje to utworzenie nowego konta na koncie służbowym firmy (dzierżawy usługi Azure AD), a nie tylko na koncie Centrum partnerskiego. Jeśli korzystasz głównie z tej aplikacji usługi Azure AD na potrzeby uwierzytelniania w centrum partnerskim i nie potrzebujesz dostępu do nich bezpośrednio, możesz wprowadzić dowolny prawidłowy adres **URL odpowiedzi** i **Identyfikator URI identyfikatora aplikacji**, o ile te wartości nie są używane przez żadną inną aplikację usługi Azure AD w katalogu.

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj aplikacje usługi Azure AD**.
1. Na następnej stronie wybierz pozycję **Nowa aplikacja usługi Azure AD**.
1. Wprowadź **adres URL odpowiedzi** dla nowej aplikacji usługi Azure AD. Jest to adres URL, pod którym użytkownicy mogą się zalogować i korzystać z aplikacji usługi Azure AD (czasami znanego również jako adres URL aplikacji lub adres URL Sign-On). *Adres URL odpowiedzi* nie może mieć więcej niż 256 znaków i musi być unikatowy w obrębie katalogu.
1. Wprowadź **Identyfikator URI aplikacji** dla nowej aplikacji usługi Azure AD. Jest to identyfikator logiczny dla aplikacji usługi Azure AD, który jest prezentowany podczas wysyłania żądania logowania jednokrotnego do usługi Azure AD. *Identyfikator URI identyfikatora aplikacji* musi być unikatowy dla każdej aplikacji usługi Azure AD w katalogu. Ten identyfikator nie może zawierać więcej niż 256 znaków. Aby uzyskać więcej informacji o IDENTYFIKATORze URI aplikacji, zobacz [Integrowanie aplikacji z Azure Active Directory](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts).
1. W sekcji **role** Określ role lub dostosowane uprawnienia dla aplikacji usługi Azure AD.
1. Wybierz pozycję **Zapisz**.

Po dodaniu lub utworzeniu aplikacji usługi Azure AD możesz powrócić do sekcji **Użytkownicy** i wybrać nazwę aplikacji, aby przejrzeć ustawienia aplikacji, w tym identyfikator dzierżawy, identyfikator klienta, adres URL odpowiedzi i identyfikator URI aplikacji.

## <a name="remove-an-azure-ad-application"></a>Usuwanie aplikacji usługi Azure AD

Aby usunąć aplikację z konta służbowego (dzierżawy usługi Azure AD), przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz aplikację, którą chcesz usunąć, przy użyciu pola wyboru w prawej kolumnie, a następnie wybierz pozycję **Usuń** z dostępnych akcji. Zostanie wyświetlone okno podręczne, aby potwierdzić, że chcesz usunąć wybrane aplikacje.

## <a name="manage-keys-for-an-azure-ad-application"></a>Zarządzanie kluczami dla aplikacji usługi Azure AD

Jeśli aplikacja usługi Azure AD odczytuje i zapisuje dane w Microsoft Azure AD, będzie potrzebny klucz. Możesz utworzyć klucze dla aplikacji usługi Azure AD, edytując jej informacje w centrum partnerskim. Możesz również usunąć klucze, które nie są już potrzebne.

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz nazwę aplikacji usługi Azure AD. Zobaczysz wszystkie aktywne klucze dla aplikacji usługi Azure AD, w tym datę utworzenia klucza i czas wygaśnięcia 50.
1. Aby usunąć klucz, który nie jest już wymagany, wybierz pozycję **Usuń**.
1. Aby dodać nowy klucz, wybierz pozycję **Dodaj nowy klucz**.
1. Zobaczysz ekran pokazujący **Identyfikator klienta** i **wartości klucza**. Pamiętaj, aby wydrukować lub skopiować te informacje, ponieważ nie będzie można uzyskać do niej dostępu, gdy opuścisz Tę stronę.
1. Jeśli chcesz utworzyć więcej kluczy, wybierz pozycję **Dodaj inny klucz**.
