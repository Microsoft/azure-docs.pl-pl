---
title: Zarządzanie użytkownikami i rolami w Azure IoT Central zarządzania | Microsoft Docs
description: Jak zarządzać użytkownikami i rolami w aplikacji Azure IoT Central jako administrator
author: lmasieri
ms.author: lmasieri
ms.date: 04/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: cff8830d180b0c234e54f7578ed9fafafeb598f0
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719173"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Zarządzanie użytkownikami i rolami w aplikacji usługi IoT Central

W tym artykule opisano, jak administrator może dodawać, edytować i usuwać użytkowników w Azure IoT Central aplikacji. W tym artykule opisano również sposób zarządzania rolami w aplikacji.

Aby uzyskać dostęp do sekcji **Administracja** i korzystać z tej sekcji, musisz mieć rolę **Administrator** dla Azure IoT Central aplikacji. Jeśli utworzysz aplikację Azure IoT Central, zostaniesz automatycznie dodany do roli **Administrator** dla tej aplikacji.

## <a name="add-users"></a>Dodawanie użytkowników

Każdy użytkownik musi mieć konto użytkownika, aby można było zalogować się i uzyskać dostęp do aplikacji. IoT Central obecnie obsługuje konta Microsoft i konta Azure Active Directory, ale nie Azure Active Directory grup.

Aby uzyskać więcej informacji, [zobacz konto Microsoft i Szybki](https://support.microsoft.com/products/microsoft-account?category=manage-account)  [start: dodawanie nowych](../../active-directory/fundamentals/add-users-azure-active-directory.md)użytkowników do Azure Active Directory .

1. Aby dodać użytkownika do aplikacji IoT Central, przejdź do strony **Użytkownicy** w **sekcji Administracja.**

  :::image type="content" source="media/howto-manage-users-roles/manage-users-pnp.png" alt-text="Zarządzanie użytkownikami":::

1. Aby dodać użytkownika, na stronie **Użytkownicy** wybierz pozycję **+ Dodaj użytkownika.**

1. Wybierz rolę dla użytkownika z **menu** rozwijanego Rola. Dowiedz się więcej o rolach w [sekcji Zarządzanie rolami](#manage-roles) tego artykułu.

  :::image type="content" source="media/howto-manage-users-roles/add-user-pnp.png" alt-text="Dodaj użytkownika i wybierz rolę.":::

  > [!NOTE]
  > Użytkownik, który jest w roli niestandardowej, która przyznaje im uprawnienia do dodawania innych użytkowników, może dodawać użytkowników do roli tylko z uprawnieniami o takich samych lub mniejszej liczbie uprawnień niż ich rola.

  > [!NOTE]
  > Jeśli użytkownik zostanie usunięty z Azure Active Directory, a następnie dodany z powrotem, nie będzie mógł zalogować się do IoT Central aplikacji. Aby ponownie włączyć dostęp, administrator aplikacji powinien również usunąć i ponownie dodać użytkownika w aplikacji.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Edytowanie ról przypisanych do użytkowników

Ról nie można zmienić po ich przypisaniu. Aby zmienić rolę przypisaną do użytkownika, usuń go, a następnie dodaj go ponownie z inną rolą.

> [!NOTE]
> Przypisane role są specyficzne dla aplikacji IoT Central i nie można nimi zarządzać z witryny Azure Portal.

## <a name="delete-users"></a>Usuwanie użytkowników

Aby usunąć użytkowników, zaznacz co najmniej jedno pole wyboru na **stronie** Użytkownicy. Następnie wybierz pozycję **Usuń**.

## <a name="manage-roles"></a>Zarządzanie rolami

Role umożliwiają kontrolowanie, kto w organizacji może wykonywać różne zadania w IoT Central. Istnieją trzy wbudowane role, które można przypisać do użytkowników aplikacji. Możesz również [tworzyć role niestandardowe,](#create-a-custom-role) jeśli potrzebujesz precyzyjnej kontroli.

> [!div class="mx-imgBorder"]
> ![Zarządzanie wyborem ról](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Administrator

Użytkownicy z rolą **Administrator** mogą kontrolować każdą część aplikacji, w tym rozliczenia, i zarządzać nimi.

Użytkownik, który tworzy aplikację, jest automatycznie przypisywany do **roli Administrator.** W roli Administrator musi zawsze znajdować się **co najmniej jeden** użytkownik.

### <a name="builder"></a>Konstruktor

Użytkownicy w roli **konstruktora** mogą zarządzać każdą częścią aplikacji, ale nie mogą wprowadzać zmian na kartach Administracja ani Ciągły eksport danych.

### <a name="operator"></a>Operator

Użytkownicy z rolą **Operator** mogą monitorować kondycję i stan urządzenia. Nie mogą oni wprowadzać zmian w szablonach urządzeń ani administrować aplikacją. Operatorzy mogą dodawać i usuwać urządzenia, zarządzać zestawami urządzeń oraz uruchamiać analizy i zadania.

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Jeśli twoje rozwiązanie wymaga precyzyjnej kontroli dostępu, możesz tworzyć role z niestandardowymi zestawami uprawnień. Aby utworzyć rolę niestandardową, przejdź do **strony Role** w **sekcji Administracja** aplikacji. Następnie wybierz **pozycję + Nowa rola** i dodaj nazwę i opis roli. Wybierz uprawnienia wymagane przez twoją rolę, a następnie wybierz pozycję **Zapisz.**

Użytkowników można dodawać do roli niestandardowej w taki sam sposób, jak w przypadku dodawania użytkowników do roli wbudowanej.

> [!div class="mx-imgBorder"]
> ![Tworzenie roli niestandardowej](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opcje roli niestandardowej

Podczas definiowania roli niestandardowej należy wybrać zestaw uprawnień udzielanych użytkownikowi, jeśli jest on członkiem roli. Niektóre uprawnienia są zależne od innych. Jeśli na przykład dodasz uprawnienie Aktualizuj osobiste pulpity  nawigacyjne do roli, uprawnienie Wyświetlanie osobistych pulpitów nawigacyjnych zostanie dodane automatycznie.  Poniższe tabele zawierają podsumowanie dostępnych uprawnień i ich zależności, których można użyć podczas tworzenia ról niestandardowych.

#### <a name="managing-devices"></a>Zarządzanie urządzeniami

**Uprawnienia szablonu urządzenia**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Zarządzanie | Widok <br/> Inne zależności: Wyświetlanie wystąpień urządzeń  |
| Pełna kontrola | Wyświetlanie, zarządzanie <br/> Inne zależności: Wyświetlanie wystąpień urządzeń |

**Uprawnienia wystąpienia urządzenia**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: Wyświetlanie szablonów urządzeń i grup urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń i grup urządzeń  |
| Utwórz | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń i grup urządzeń  |
| Usuń | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń i grup urządzeń  |
| Wykonywanie poleceń | Aktualizacja, widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń i grup urządzeń  |
| Wyświetlanie danych pierwotnych | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń i grup urządzeń  |
| Pełna kontrola | Polecenia View, Update, Create, Delete, Execute, View raw data <br/> Inne zależności: Wyświetlanie szablonów urządzeń i grup urządzeń  |

**Uprawnienia grup urządzeń**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: Wyświetlanie szablonów urządzeń i wystąpień urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń i wystąpień urządzeń   |
| Utwórz | Wyświetlanie, aktualizowanie <br/> Inne zależności: Wyświetlanie szablonów urządzeń i wystąpień urządzeń   |
| Usuń | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń i wystąpień urządzeń  |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie, usuwanie <br/> Inne zależności: Wyświetlanie szablonów urządzeń i wystąpień urządzeń |

**Uprawnienia do zarządzania łącznością urządzeń**

| Nazwa | Zależności |
| ---- | -------- |
| Odczytywanie wystąpienia | Brak <br/> Inne zależności: Wyświetlanie szablonów urządzeń, grup urządzeń, wystąpień urządzeń |
| Zarządzanie wystąpieniem | Odczytywanie wystąpienia <br /> Inne zależności: Wyświetlanie szablonów urządzeń, grup urządzeń, wystąpień urządzeń |
| Odczyt globalny | Brak   |
| Zarządzanie globalne | Odczyt globalny |
| Pełna kontrola | Odczyt wystąpienia, Zarządzanie wystąpieniem, Odczyt globalny, Zarządzanie globalne <br/> Inne zależności: Wyświetlanie szablonów urządzeń, grup urządzeń, wystąpień urządzeń |

**Uprawnienia zadań**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: Wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Utwórz | Wyświetlanie, aktualizowanie <br/> Inne zależności: Wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Usuń | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń |
| Wykonaj polecenie | Widok <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń; Aktualizowanie wystąpień urządzeń; Wykonywanie poleceń w wystąpieniach urządzeń |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie, usuwanie, wykonywanie <br/> Inne zależności: wyświetlanie szablonów urządzeń, wystąpień urządzeń i grup urządzeń; Aktualizowanie wystąpień urządzeń; Wykonywanie poleceń w wystąpieniach urządzeń |

**Uprawnienia reguł**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: Wyświetlanie szablonów urządzeń |
| Aktualizacja | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń |
| Utwórz | Wyświetlanie, aktualizowanie <br/> Inne zależności: Wyświetlanie szablonów urządzeń |
| Usuń | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie, usuwanie <br/> Inne zależności: Wyświetlanie szablonów urządzeń |

#### <a name="managing-the-app"></a>Zarządzanie aplikacją

**Uprawnienia ustawień aplikacji**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Kopiuj | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń, wystąpień urządzeń, grup urządzeń, pulpitów nawigacyjnych, eksportu danych, brandowania, linków pomocy, ról niestandardowych, reguł |
| Usuń | Widok   |
| Pełna kontrola | Wyświetlanie, aktualizowanie, kopiowanie, usuwanie <br/> Inne zależności: Wyświetlanie szablonów urządzeń, grup urządzeń, pulpitów nawigacyjnych aplikacji, eksportu danych, brandowania, linków pomocy, ról niestandardowych, reguł |

**Uprawnienia do eksportowania szablonu aplikacji**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Eksportowanie | Widok <br/> Inne zależności: Wyświetlanie szablonów urządzeń, wystąpień urządzeń, grup urządzeń, pulpitów nawigacyjnych, eksportu danych, brandowania, linków pomocy, ról niestandardowych, reguł |
| Pełna kontrola | Wyświetlanie, eksportowanie <br/> Inne zależności: Wyświetlanie szablonów urządzeń, grup urządzeń, pulpitów nawigacyjnych aplikacji, eksportu danych, brandowania, linków pomocy, ról niestandardowych, reguł |

**Uprawnienia do przekazywania plików urządzenia**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Zarządzanie | Widok   |
| Pełna kontrola | Wyświetlanie, zarządzanie |

**Uprawnienia do rozliczeń**

| Nazwa | Zależności |
| ---- | -------- |
| Zarządzanie | Brak     |
| Pełna kontrola | Zarządzanie |

#### <a name="managing-users-and-roles"></a>Zarządzanie użytkownikami i rolami

**Uprawnienia ról niestandardowych**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak |
| Aktualizacja | Widok |
| Utwórz | Wyświetlanie, aktualizowanie |
| Usuń | Widok |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie, usuwanie |

**Uprawnienia do zarządzania użytkownikami**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak <br/> Inne zależności: Wyświetlanie ról niestandardowych |
| Dodaj | Widok <br/> Inne zależności: Wyświetlanie ról niestandardowych |
| Usuń | Widok <br/> Inne zależności: Wyświetlanie ról niestandardowych |
| Pełna kontrola | Wyświetlanie, dodawanie, usuwanie <br/> Inne zależności: Wyświetlanie ról niestandardowych |

> [!NOTE]
> Użytkownik pełniący rolę niestandardową, który przyznaje im uprawnienia do dodawania innych użytkowników, może dodawać użytkowników tylko do roli z taką samą lub mniejszą liczbą uprawnień niż ich rola.

#### <a name="customizing-the-app"></a>Dostosowywanie aplikacji

**Uprawnienia pulpitu nawigacyjnego aplikacji**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Utwórz | Wyświetlanie, aktualizowanie |
| Usuń | Widok   |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie, usuwanie |

**Uprawnienia osobistych pulpitów nawigacyjnych**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Utwórz | Wyświetlanie, aktualizowanie   |
| Usuń | Widok   |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie, usuwanie |

**Uprawnienia do brandingu, faviconu i kolorów**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Pełna kontrola | Wyświetlanie, aktualizowanie |

**Pomoc : uprawnienia linków**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Pełna kontrola | Wyświetlanie, aktualizowanie |

#### <a name="extending-the-app"></a>Rozszerzanie aplikacji

**Uprawnienia do eksportowania danych**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak     |
| Aktualizacja | Widok   |
| Utwórz | Wyświetlanie, aktualizowanie  |
| Usuń | Widok   |
| Pełna kontrola | Wyświetlanie, aktualizowanie, tworzenie, usuwanie |

**Uprawnienia tokenu interfejsu API**

| Nazwa | Zależności |
| ---- | -------- |
| Widok | Brak  <br/> Inne zależności: Wyświetlanie ról niestandardowych |
| Utwórz | Widok <br/> Inne zależności: Wyświetlanie ról niestandardowych |
| Usuń | Widok <br/> Inne zależności: Wyświetlanie ról niestandardowych |
| Pełna kontrola | Wyświetlanie, tworzenie, usuwanie <br/> Inne zależności: Wyświetlanie ról niestandardowych |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zarządzać użytkownikami i rolami w aplikacji IoT Central, sugerowanym następnym krokiem jest nauka zarządzania [rachunkiem.](howto-view-bill.md)