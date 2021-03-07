---
title: Adaptacyjne Zabezpieczanie sieci w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak używać rzeczywistych wzorców ruchu do ochrony reguł sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń), a także ulepszania stan zabezpieczeń.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: 14523e814c85469aa02a860e87b86defd7823c16
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439599"
---
# <a name="improve-your-network-security-posture-with-adaptive-network-hardening"></a>Ulepszanie zabezpieczeń sieci stan przy użyciu adaptacyjnej funkcjonalności sieci
Dowiedz się, jak konfigurować adaptacyjną ochronę sieci w Security Center.

## <a name="availability"></a>Dostępność
|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność (GA)|
|Wpisaną|Wymaga [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md)|
|Wymagane role i uprawnienia:|Uprawnienia do zapisu na sieciowych grup zabezpieczeń komputera|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||

## <a name="what-is-adaptive-network-hardening"></a>Co to jest adaptacyjna Funkcja ograniczania przepustowości sieci?
Zastosowanie [sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń)](../virtual-network/network-security-groups-overview.md) do filtrowania ruchu do i z zasobów, usprawnia stan zabezpieczeń sieci. Nadal jednak mogą istnieć sytuacje, w których rzeczywisty ruch przepływający przez sieciowej grupy zabezpieczeń jest podzbiorem zdefiniowanych reguł sieciowej grupy zabezpieczeń. W takich przypadkach dalsze ulepszanie stan zabezpieczeń można osiągnąć przez zaostrzonie reguł sieciowej grupy zabezpieczeń na podstawie rzeczywistych wzorców ruchu.

Adaptacyjne zwiększanie przepustowości sieci zapewnia rekomendacje w celu dalszej ochrony reguł sieciowej grupy zabezpieczeń. Używa algorytmu uczenia maszynowego, który ma czynniki w rzeczywistym ruchu, znanej zaufanej konfiguracji, analizie zagrożeń i innych wskaźnikach naruszenia, a następnie udostępnia zalecenia zezwalające na ruch tylko z konkretnych krotek IP/portów.

Załóżmy na przykład, że istniejąca reguła sieciowej grupy zabezpieczeń ma zezwalać na ruch z 140.20.30.10/24 na porcie 22. W oparciu o analizę ruchu, adaptacyjne Zabezpieczanie sieci może zalecać zawężenie zakresu, aby umożliwić ruch z 140.23.30.10/29 i odmówić całego ruchu do tego portu.

>[!Note]
> Zalecenia dotyczące ograniczania przepustowości sieci są obsługiwane tylko na następujących portach (dla protokołów UDP i TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


## <a name="view-and-manage-hardening-alerts-and-rules"></a>Wyświetl alerty i reguły ograniczania i zarządzaj nimi

1. Z menu Security Center, Otwórz pulpit nawigacyjny **usługi Azure Defender** i wybierz kafelek adaptacyjnej ochrony sieci (1) lub element panelu usługi Insights związany z podadaptacyjną funkcjonalnością sieci (2). 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="Uzyskiwanie dostępu do narzędzi adaptacyjnego zabezpieczania sieci" lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > Panel Insights (szczegółowe informacje) pokazuje procent maszyn wirtualnych, które są aktualnie obronne dzięki adaptacyjnej funkcjonalności sieci. 

1. Na stronie szczegółów **zalecanych zaleceń dotyczących ograniczania przepustowości sieci należy zastosować zalecenie dotyczące maszyn wirtualnych** mających dostęp do Internetu. spowoduje to otwarcie z sieci maszyny wirtualne pogrupowane na trzy karty:
   * **Zasoby w złej kondycji**: maszyny wirtualne, które aktualnie mają zalecenia i alerty, które zostały wyzwolone przez uruchomienie adaptacyjnego algorytmu ograniczania przepustowości sieci. 
   * **Dobra kondycja**: maszyny wirtualne bez alertów i zaleceń.
   * **Niezeskanowane zasoby**: maszyny wirtualne, na których algorytm ograniczania funkcjonalności sieci nie może zostać uruchomiony z jednego z następujących powodów:
      * **Maszyny wirtualne są klasycznymi maszynami wirtualnymi**: obsługiwane są tylko Azure Resource Manager maszyny wirtualne.
      * Brak **wystarczającej ilości danych**: w celu wygenerowania dokładnego zalecenia dotyczącego ograniczania ruchu, Security Center wymaga co najmniej 30 dni danych ruchu.
      * **Maszyna wirtualna nie jest chroniona przez usługę Azure Defender**: dla tej funkcji kwalifikują się tylko maszyny wirtualne chronione za pomocą [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md) .

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="Strona szczegółów rekomendacji rekomendacji dotyczącej ograniczania przepustowości sieci należy stosować w przypadku maszyn wirtualnych mających dostęp do Internetu.":::

1. Na karcie **zasoby w złej kondycji** wybierz maszynę wirtualną, aby wyświetlić jej alerty i zalecane reguły ograniczania funkcjonalności, które mają zostać zastosowane.

    - Na karcie **reguły** znajduje się lista reguł, które zaleca się dodanie
    - Na karcie **alerty** znajduje się lista alertów, które zostały wygenerowane z powodu ruchu, przepływających do zasobu, który nie należy do zakresu adresów IP dozwolony w zalecanych regułach.

1. Opcjonalnie można edytować reguły:

    - [Modyfikowanie reguły](#modify-rule)
    - [Usuwanie reguły](#delete-rule) 
    - [Dodaj regułę](#add-rule)

3. Wybierz reguły, które chcesz zastosować w sieciowej grupy zabezpieczeń, a następnie kliknij pozycję **Wymuszaj**.

    > [!TIP]
    > Jeśli dozwolone źródłowe zakresy adresów IP są wyświetlane jako "Brak", oznacza to, że zalecaną regułą jest reguła *Odmów* , w przeciwnym razie jest to reguła *zezwalania* .

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="Zarządzanie regułami adaptacyjnego zabezpieczania sieci":::

      > [!NOTE]
      > Zasady wymuszane są dodawane do sieciowej grupy ZABEZPIECZEŃów chroniących maszynę wirtualną. (Maszyna wirtualna może być chroniona przez sieciowej grupy zabezpieczeń, która jest skojarzona z kartą sieciową lub podsieć, w której znajduje się maszyna wirtualna, lub obie)

### <a name="modify-a-rule"></a>Modyfikowanie reguły <a name ="modify-rule"></a>

Możesz chcieć zmodyfikować parametry reguły, która została zalecana. Na przykład możesz chcieć zmienić zalecane zakresy adresów IP.

Niektóre ważne wskazówki dotyczące modyfikowania reguły ograniczania funkcjonalności sieci:

* Można modyfikować parametry tylko dla reguł "Zezwalaj". 
* Nie można zmienić reguł "Zezwalaj" na "Odmów" reguł. 

  > [!NOTE]
  > Tworzenie i modyfikowanie reguł "Odmów" jest wykonywane bezpośrednio w sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń](../virtual-network/manage-network-security-group.md).

* Reguła **Odmów całego ruchu** jest jedynym typem reguły "Odmów", która będzie wyświetlana w tym miejscu, i nie może być modyfikowana. Można jednak go usunąć (zobacz [Usuwanie reguły](#delete-rule)).
  > [!NOTE]
  > Reguła **Odmów całego ruchu** jest zalecana, gdy w wyniku uruchomienia algorytmu Security Center nie zidentyfikuje ruchu, który powinien być dozwolony, na podstawie istniejącej konfiguracji sieciowej grupy zabezpieczeń. W związku z tym zalecaną regułą jest odmowa całego ruchu kierowanego do określonego portu. Nazwa tego typu reguły jest wyświetlana jako "*wygenerowany przez system*". Po wymuszeniu tej reguły jej rzeczywista nazwa w sieciowej grupy zabezpieczeń będzie ciągiem zawierającym Protokół, kierunek ruchu, "Odmów" i liczbę losową.

*Aby zmodyfikować adaptacyjną zasadę ograniczania funkcjonalności sieci:*

1. Aby zmodyfikować niektóre parametry reguły, na karcie **reguły** kliknij trzy kropki (...) na końcu wiersza reguły, a następnie kliknij pozycję **Edytuj**.

   ![Reguła edycji s](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. W oknie **Edytowanie reguły** Zaktualizuj szczegóły, które chcesz zmienić, a następnie kliknij przycisk **Zapisz**.

   > [!NOTE]
   > Po kliknięciu przycisku **Zapisz** pomyślnie zmieniono regułę. *Nie została jednak zastosowana do sieciowej grupy zabezpieczeń.* Aby go zastosować, należy wybrać regułę z listy i wybrać pozycję **Wymuszaj** (zgodnie z opisem w następnym kroku).

   ![Wybieranie pozycji Zapisz](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Aby zastosować zaktualizowaną regułę, wybierz ją z listy, a następnie kliknij pozycję **Wymuszaj**.

    ![Wymuś regułę](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Dodaj nową regułę <a name ="add-rule"></a>

Można dodać regułę "Zezwalaj", która nie jest zalecana przez Security Center.

> [!NOTE]
> Tutaj można dodawać tylko reguły "Zezwalaj". Jeśli chcesz dodać reguły "Odmów", możesz to zrobić bezpośrednio w sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń](../virtual-network/manage-network-security-group.md).

*Aby dodać zasadę ograniczania funkcjonalności sieci:*

1. Kliknij pozycję **Dodaj regułę** (znajdującą się w lewym górnym rogu).

   ![dodawanie reguły](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. W oknie **Nowa reguła** wprowadź szczegóły, a następnie kliknij przycisk **Dodaj**.

   > [!NOTE]
   > Po kliknięciu przycisku **Dodaj** reguła została pomyślnie dodana i zostanie wyświetlona na liście z innymi zalecanymi regułami. Nie została jednak zastosowana w sieciowej grupy zabezpieczeń. Aby go uaktywnić, należy wybrać regułę z listy, a następnie kliknąć pozycję **Wymuszaj** (zgodnie z opisem w następnym kroku).

3. Aby zastosować nową regułę, z listy wybierz nową regułę, a następnie kliknij pozycję **Wymuszaj**.

    ![Wymuś regułę](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Usuwanie reguły <a name ="delete-rule"></a>

W razie potrzeby można usunąć zalecaną regułę dla bieżącej sesji. Na przykład można określić, że stosowanie sugerowanej reguły może blokować wiarygodny ruch.

*Aby usunąć regułę adaptacyjnej ochrony sieci dla bieżącej sesji:*

1. Na karcie **reguły** kliknij trzy kropki (...) na końcu wiersza reguły, a następnie kliknij pozycję **Usuń**.  

    ![Usuwanie reguły](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)