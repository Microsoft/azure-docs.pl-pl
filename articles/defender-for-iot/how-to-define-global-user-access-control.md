---
title: Definiowanie globalnej kontroli dostępu użytkowników
description: W dużych organizacjach uprawnienia użytkowników mogą być złożone i mogą być określane przez globalną strukturę organizacyjną, oprócz standardowej struktury lokacji i strefy.
ms.date: 12/08/2020
ms.topic: article
ms.openlocfilehash: 8f5f8df56a5dcb4152fc0ae9fcae3d504d6cf3e2
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784292"
---
# <a name="define-global-access-control"></a>Definiowanie globalnej kontroli dostępu

W dużych organizacjach uprawnienia użytkowników mogą być złożone i mogą być określane przez globalną strukturę organizacyjną, oprócz standardowej struktury lokacji i strefy.

Aby obsługiwać zapotrzebowanie na uprawnienia dostępu użytkowników, które są globalne i bardziej złożone, można utworzyć globalną topologię biznesową opartą na jednostkach, regionach i lokacjach firmy. Następnie można zdefiniować uprawnienia dostępu użytkowników wokół tych jednostek.

Współpraca z narzędziami dostępu do topologii biznesowej ułatwia organizacjom implementowanie strategii z zerowym zaufaniem przez lepszą kontrolę nad tym, gdzie użytkownicy zarządzają i analizują urządzenia w usłudze Azure Defender for IoT.

## <a name="about-access-groups"></a>Informacje o grupach dostępu

Globalna kontrola dostępu jest ustanawiana za pomocą tworzenia grup dostępu użytkowników. Grupy dostępu składają się z reguł, w których użytkownicy mogą uzyskiwać dostęp do określonych jednostek biznesowych. Praca z grupami pozwala kontrolować dostęp do widoku i konfiguracji usługi Defender for IoT dla konkretnych ról użytkowników w odpowiednich jednostkach, regionach i lokacjach firmy.

Na przykład Zezwól analitykom zabezpieczeń z grupy Active Directory na dostęp do wszystkich zachodnich i szklanych linii produkcyjnych, a także linii sztucznej w jednym regionie.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Diagram grupy Active Directory analityków zabezpieczeń.":::

Przed utworzeniem grup dostępu zalecamy:

- Starannie Skonfiguruj topologię biznesową. Aby uzyskać więcej informacji na temat topologii biznesowej, zobacz [Praca z widokami mapy witryny](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views).

- Zaplanuj, którzy użytkownicy są powiązani z tworzonymi grupami dostępu. Do przypisywania użytkowników do grup dostępu są dostępne dwie opcje:

  - **Przypisywanie grup Active Directory**: Upewnij się, że skonfigurowano wystąpienie Active Directory do integracji z lokalną konsolą zarządzania.
  
  - **Przypisywanie użytkowników lokalnych**: Upewnij się, że utworzono użytkowników. Aby uzyskać więcej informacji, zobacz [Definiowanie użytkowników](how-to-create-and-manage-users.md#define-users).

Użytkowników administracyjnych nie można przypisać do grup dostępu. Ci użytkownicy mają domyślnie dostęp do wszystkich jednostek topologii biznesowej.

## <a name="create-access-groups"></a>Tworzenie grup dostępu

W tej sekcji opisano sposób tworzenia grup dostępu. Domyślne globalne jednostki biznesowe i regiony są tworzone dla pierwszej tworzonej grupy. Można edytować domyślne jednostki podczas definiowania pierwszej grupy.

Aby utworzyć grupy:

1. Wybierz pozycję **grupy dostępu** z menu po stronie lokalnej konsoli zarządzania.

2. Wybierz pozycję :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::. W oknie dialogowym **Dodawanie grupy dostępu** wprowadź nazwę grupy dostępu. Konsola obsługuje 64 znaków. Przypisz nazwę w sposób, który pomoże Ci łatwo odróżnić tę grupę od innych grup.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Okno dialogowe Dodaj grupę dostępu, w którym można tworzyć grupy dostępu.":::

3. Jeśli zostanie wyświetlona opcja **Przypisz grupę Active Directory** , można przypisać jedną grupę Active Directory użytkowników do tej grupy dostępu.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="W oknie dialogowym Tworzenie grupy dostępu Przypisz grupę Active Directory.":::

   Jeśli ta opcja nie zostanie wyświetlona i chcesz uwzględnić grupy Active Directory w grupach dostępu, wybierz pozycję **Ustawienia systemowe**. W okienku **integracje** Zdefiniuj grupy. Wprowadź nazwę grupy dokładnie tak, jak jest wyświetlana w konfiguracjach Active Directory i małymi literami.

5. W okienku **Użytkownicy** Przypisz tylu użytkownikom jako wymagane do grupy. Możesz również przypisać użytkowników do różnych grup. Jeśli pracujesz w ten sposób, musisz utworzyć i zapisać grupę i reguły dostępu, a następnie przypisać użytkowników do grupy z okienka **Użytkownicy** .

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Zarządzaj rolami użytkowników i przypisuj je zgodnie z potrzebami.":::

6. Utwórz reguły w oknie dialogowym **Dodaj reguły dla *nazwy*** na podstawie wymagań dostępu topologii biznesowej. Opcje, które są wyświetlane w tym miejscu, są zależne od topologii zaprojektowanej w oknach **Widok przedsiębiorstwa** i **Zarządzanie lokacjami** . 

   Można utworzyć więcej niż jedną regułę dla każdej grupy. Podczas pracy ze szczegółowym rozdrobnieniem dostępu w wielu lokacjach może być konieczne utworzenie więcej niż jednej reguły na grupę. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Dodaj regułę do systemu.":::

Utworzone reguły pojawiają się w oknie dialogowym **Dodaj grupę dostępu** . Tam możesz je usunąć lub edytować.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Wyświetlanie i edytowanie wszystkich grup dostępu przy użyciu tego okna.":::

### <a name="about-rules"></a>Reguły — informacje

Podczas tworzenia reguł należy zwrócić uwagę na następujące informacje:

- Gdy grupa dostępu zawiera kilka reguł, logika reguły agreguje wszystkie reguły. Na przykład reguły używają logiki, nie lub logiki.

- Aby reguła została pomyślnie zastosowana, należy przypisać czujniki do stref w oknie **Zarządzanie lokacjami** .

- Można przypisać tylko jeden element dla każdej reguły. Na przykład można przypisać jedną jednostkę biznesową, jeden region i jedną lokację dla każdej reguły. Utwórz więcej reguł dla grupy, jeśli na przykład chcesz, aby użytkownicy z jednej grupy Active Directory mieli dostęp do różnych jednostek gospodarczych w różnych regionach.

- Jeśli zmienisz jednostkę, a zmiana ma wpływ na logikę reguły, reguła zostanie usunięta. Jeśli zmiany wprowadzone do jednostki topologii wpływają na logikę reguły w taki sposób, że wszystkie reguły zostaną usunięte, Grupa dostępu pozostaje niezmieniona, ale użytkownicy nie będą mogli zalogować się do lokalnej konsoli zarządzania. Użytkownicy są powiadamiani, aby skontaktować się z administratorem w celu zalogowania się.

- Jeśli nie zostanie wybrana żadna jednostka biznesowa lub region, użytkownicy będą mieli dostęp do wszystkich zdefiniowanych jednostek i regionów firmy.

## <a name="see-also"></a>Zobacz też

[Informacje o usłudze Defender dla użytkowników konsoli IoT](how-to-create-and-manage-users.md)
