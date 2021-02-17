---
title: Zabezpieczenia rozwiązań usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Poznaj najlepsze rozwiązania w zakresie zabezpieczeń dzięki usłudze Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b589f98ee78d0709b2a74ba4e364cec0e486e968
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547166"
---
# <a name="secure-azure-digital-twins"></a>Zabezpieczanie usługi Azure Digital bliźniaczych reprezentacji

W celu zapewnienia bezpieczeństwa usługa Azure Digital bliźniaczych reprezentacji zapewnia precyzyjną kontrolę dostępu do określonych danych, zasobów i akcji we wdrożeniu. Odbywa się to za pośrednictwem szczegółowej strategii zarządzania rolami i uprawnieniami o nazwie **Kontrola dostępu oparta na rolach (RBAC) platformy Azure**. W [tym miejscu](../role-based-access-control/overview.md)możesz zapoznać się z ogólnymi zasadami kontroli RBAC platformy Azure.

Usługa Azure Digital bliźniaczych reprezentacji obsługuje również szyfrowanie przechowywanych danych.

## <a name="roles-and-permissions-with-azure-rbac"></a>Role i uprawnienia za pomocą funkcji RBAC platformy Azure

Usługa Azure RBAC jest świadczona do usługi Azure Digital bliźniaczych reprezentacji za pośrednictwem integracji z usługą [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Możesz użyć kontroli RBAC platformy Azure, aby przyznać uprawnienia *podmiotowi zabezpieczeń*, które może być użytkownikiem, grupą lub jednostką usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniany przez usługę Azure AD i odbiera token OAuth 2,0 w programie Return. Token ten może służyć do autoryzowania żądania dostępu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

### <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

W przypadku usługi Azure AD dostęp jest procesem dwuetapowym. Gdy podmiot zabezpieczeń (użytkownik, Grupa lub aplikacja) próbuje uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji, żądanie musi być *uwierzytelnione* i *autoryzowane*. 

1. Najpierw jest *uwierzytelniana* tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0.
2. Następnie token jest przesyłany w ramach żądania do usługi Azure Digital bliźniaczych reprezentacji, aby *autoryzować* dostęp do określonego zasobu.

Krok uwierzytelniania wymaga, aby każde żądanie aplikacji zawierało token dostępu OAuth 2,0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak aplikacja [Azure Functions](../azure-functions/functions-overview.md) , może użyć **zarządzanej tożsamości** w celu uzyskania dostępu do zasobów. Więcej informacji na temat tożsamości zarządzanych można znaleźć w następnej sekcji.

Krok autoryzacji wymaga przypisania roli platformy Azure do podmiotu zabezpieczeń. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Usługa Azure Digital bliźniaczych reprezentacji oferuje role platformy Azure, które obejmują zestawy uprawnień dla zasobów usługi Azure Digital bliźniaczych reprezentacji. Te role są opisane w dalszej części tego artykułu.

Aby dowiedzieć się więcej o rolach i przypisaniach ról obsługiwanych na platformie Azure, zobacz [*Omówienie różnych ról*](../role-based-access-control/rbac-and-directory-admin-roles.md) w dokumentacji usługi Azure RBAC.

#### <a name="authentication-with-managed-identities"></a>Uwierzytelnianie za pomocą tożsamości zarządzanych

[Zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) to funkcja obejmująca wiele platform Azure, która umożliwia tworzenie bezpiecznej tożsamości skojarzonej z wdrożeniem, w którym działa kod aplikacji. Następnie można powiązać tę tożsamość z rolami kontroli dostępu, aby przyznać niestandardowe uprawnienia dostępu do określonych zasobów platformy Azure wymaganych przez aplikację.

Przy użyciu tożsamości zarządzanych platforma Azure zarządza tą tożsamością środowiska uruchomieniowego. Nie jest konieczne przechowywanie i ochrona kluczy dostępu w kodzie lub konfiguracji aplikacji, zarówno w przypadku tożsamości, jak i dla zasobów, do których należy uzyskać dostęp. Aplikacja kliencka usługi Azure Digital bliźniaczych reprezentacji działająca w aplikacji Azure App Service nie musi obsługiwać reguł i kluczy SAS ani żadnych innych tokenów dostępu. Aplikacja kliencka wymaga tylko adresu punktu końcowego przestrzeni nazw usługi Azure Digital bliźniaczych reprezentacji. Po nawiązaniu połączenia aplikacji usługa Azure Digital bliźniaczych reprezentacji wiąże kontekst jednostki zarządzanej z klientem. Po skojarzeniu jej z tożsamością zarządzaną klient Digital bliźniaczych reprezentacji może wykonać wszystkie autoryzowane operacje. Autoryzacja zostanie następnie przyznana przez skojarzenie zarządzanej jednostki z rolą platformy Azure Digital bliźniaczych reprezentacji na platformie Azure (opisaną poniżej).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autoryzacja: role platformy Azure dla usługi Azure Digital bliźniaczych reprezentacji

Platforma Azure udostępnia **dwie wbudowane role platformy Azure** umożliwiające autoryzowanie dostępu do [interfejsów API płaszczyzny danych](how-to-use-apis-sdks.md#overview-data-plane-apis)Digital bliźniaczych reprezentacji na platformie Azure. Można odwołać się do ról według nazwy lub identyfikatora:

| Wbudowana rola | Opis | ID (Identyfikator) | 
| --- | --- | --- |
| Właściciel danych Digital bliźniaczych reprezentacji systemu Azure | Zapewnia pełen dostęp za pośrednictwem zasobów usługi Azure Digital bliźniaczych reprezentacji | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Czytnik danych Digital bliźniaczych reprezentacji systemu Azure | Zapewnia dostęp tylko do odczytu do zasobów usługi Azure Digital bliźniaczych reprezentacji | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

Role można przypisywać na dwa sposoby:
* za pośrednictwem okienka kontroli dostępu (IAM) dla usługi Azure Digital bliźniaczych reprezentacji w Azure Portal (zobacz [*Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal*](../role-based-access-control/role-assignments-portal.md))
* za pomocą poleceń interfejsu wiersza polecenia w celu dodania lub usunięcia roli

Aby uzyskać bardziej szczegółowe instrukcje, jak to zrobić, wypróbuj w samouczku Digital bliźniaczych reprezentacji na platformie Azure [*: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md).

Aby uzyskać więcej informacji na temat sposobu definiowania wbudowanych ról, zobacz [*Omówienie definicji ról*](../role-based-access-control/role-definitions.md) w dokumentacji usługi Azure RBAC. Aby uzyskać informacje na temat tworzenia ról niestandardowych platformy Azure, zobacz [*role niestandardowe platformy Azure*](../role-based-access-control/custom-roles.md).

##### <a name="automating-roles"></a>Automatyzowanie ról

W przypadku odwoływania się do ról w zautomatyzowanych scenariuszach zalecamy odwoływanie się do nich za pomocą ich **identyfikatorów** zamiast nazw. Nazwy mogą ulec zmianie między wersjami, ale identyfikatory nie będą, dzięki czemu mają bardziej stabilne odwołanie w automatyzacji.

> [!TIP]
> Jeśli assiging role z poleceniem cmdlet, takim jak `New-AzRoleAssignment` ([odwołanie](/powershell/module/az.resources/new-azroleassignment)), możesz użyć `-RoleDefinitionId` PARAMETRU zamiast `-RoleDefinitionName` do przekazywania identyfikatora zamiast nazwy roli.

### <a name="permission-scopes"></a>Zakresy uprawnień

Przed przypisaniem roli platformy Azure do podmiotu zabezpieczeń należy określić zakres dostępu, który powinien mieć podmiot zabezpieczeń. Najlepsze rozwiązania określają, że najlepiej jest przyznać tylko najwęższy możliwy zakres.

Na poniższej liście opisano poziomy, w których można ograniczyć dostęp do zasobów usługi Azure Digital bliźniaczych reprezentacji.
* Modele: akcje dla tego zasobu wymuszają kontrolę nad [modelami](concepts-models.md) przekazanymi w usłudze Azure Digital bliźniaczych reprezentacji.
* Zapytanie Digital bliźniaczych reprezentacji Graph: akcje dla tego zasobu określają możliwość uruchamiania [operacji zapytania](concepts-query-language.md) na cyfrowym bliźniaczych reprezentacjiu w ramach grafu Digital bliźniaczych reprezentacji na platformie Azure.
* Cyfrowe sznurki: akcje dla tego zasobu zapewniają kontrolę nad operacjami CRUD na [bliźniaczych reprezentacji cyfrowym](concepts-twins-graph.md) na grafie bliźniaczym.
* Relacja Digital bliźniaczy: akcje dla tego zasobu definiują kontrolę nad operacjami CRUD na [relacjach](concepts-twins-graph.md) między cyframi cyfrowymi bliźniaczych reprezentacji na grafie bliźniaczym.
* Trasa zdarzenia: akcje dla tego zasobu określają uprawnienia do [kierowania zdarzeń](concepts-route-events.md) z usługi Azure Digital bliźniaczych reprezentacji do usługi punktu końcowego, takiej jak [centrum zdarzeń](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)lub [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Rozwiązywanie problemów z uprawnieniami

Jeśli użytkownik próbuje wykonać akcję niedozwoloną przez ich rolę, może otrzymać błąd od odczytu żądania obsługi `403 (Forbidden)` . Aby uzyskać więcej informacji i kroków rozwiązywania problemów, zobacz [*Rozwiązywanie problemów: żądanie usługi Azure Digital bliźniaczych reprezentacji nie powiodło się. stan: 403 (niedozwolone)*](troubleshoot-error-403.md).

## <a name="managed-identity-for-accessing-other-resources-preview"></a>Zarządzana tożsamość do uzyskiwania dostępu do innych zasobów (wersja zapoznawcza)

Skonfigurowanie **tożsamości zarządzanej** w usłudze [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji może umożliwić wystąpienie z łatwością uzyskiwać dostęp do innych zasobów chronionych przez usługę Azure AD, takich jak [Azure Key Vault](../key-vault/general/overview.md). Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowizacji ani rotacji żadnych wpisów tajnych. Aby uzyskać więcej informacji na temat tożsamości zarządzanych w usłudze Azure AD, zobacz  [*zarządzane tożsamości dla zasobów platformy Azure*](../active-directory/managed-identities-azure-resources/overview.md). 

Platforma Azure obsługuje dwa typy zarządzanych tożsamości: przypisane do systemu i przypisane przez użytkownika. Obecnie usługa Azure Digital bliźniaczych reprezentacji obsługuje tylko **tożsamości przypisane do systemu**. 

Do uwierzytelniania w ramach [niestandardowego punktu końcowego](concepts-route-events.md#create-an-endpoint)platformy Azure można użyć zarządzanej tożsamości przypisanej do systemu. Usługa Azure Digital bliźniaczych reprezentacji obsługuje uwierzytelnianie oparte na tożsamościach przypisane do punktów końcowych dla [centrów zdarzeń](../event-hubs/event-hubs-about.md) i [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   miejsc docelowych oraz punkt końcowy [kontenera usługi Azure Storage](../storage/blobs/storage-blobs-introduction.md)   dla [zdarzeń utraconych](concepts-route-events.md#dead-letter-events). [Event Grid](../event-grid/overview.md)   punkty końcowe nie są obecnie obsługiwane dla tożsamości zarządzanych.

Aby uzyskać instrukcje dotyczące włączania tożsamości zarządzanej przez system dla usługi Azure Digital bliźniaczych reprezentacji i używania jej do kierowania zdarzeń, zobacz [*How to: Enable Managed Identity for Routing Events (wersja zapoznawcza)*](how-to-enable-managed-identities.md).

## <a name="private-network-access-with-azure-private-link-preview"></a>Dostęp do sieci prywatnej za pomocą prywatnego linku platformy Azure (wersja zapoznawcza)

[Link prywatny platformy Azure](../private-link/private-link-overview.md) to usługa, która umożliwia dostęp do zasobów platformy Azure (takich jak [Azure Event Hubs](../event-hubs/event-hubs-about.md), [Azure Storage](../storage/common/storage-introduction.md)i [Azure Cosmos DB](../cosmos-db/introduction.md)) oraz usług klienta i partnerskich hostowanych na platformie Azure za pośrednictwem prywatnego punktu końcowego w [usłudze Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md). 

Analogicznie, można użyć prywatnych punktów końcowych dla wystąpienia z cyfrowym przędzą na platformie Azure, aby umożliwić klientom znajdującym się w sieci wirtualnej bezpieczny dostęp do wystąpienia za pośrednictwem prywatnego linku. 

Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej platformy Azure. Ruch sieciowy między klientem w sieci prywatnej a wystąpieniem usługi Azure Digital bliźniaczych reprezentacji przechodzi przez sieć wirtualną oraz link prywatny w sieci szkieletowej firmy Microsoft, eliminując narażenie na publiczny Internet. Oto wizualna reprezentacja tego systemu:

:::image type="content" source="media/concepts-security/private-link.png" alt-text="Diagram przedstawiający sieć firmy PowerGrid, która jest chronioną siecią wirtualną bez dostępu do Internetu/chmury publicznej, nawiązując połączenie za pomocą prywatnego linku do wystąpienia usługi Azure Digital bliźniaczych reprezentacji o nazwie CityOfTwins.":::

Skonfigurowanie prywatnego punktu końcowego dla swojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji umożliwia zabezpieczenie wystąpienia usługi Azure Digital bliźniaczych reprezentacji i wyeliminowanie ryzyka publicznego, a także uniknięcie eksfiltracji danych z sieci wirtualnej.

Aby uzyskać instrukcje dotyczące konfigurowania prywatnego linku do usługi Azure Digital bliźniaczych reprezentacji, zobacz [*How to: Enable Private Access with Private link (wersja zapoznawcza)*](how-to-enable-private-link.md).

### <a name="design-considerations"></a>Zagadnienia dotyczące projektowania 

W przypadku korzystania z prywatnego linku do usługi Azure Digital bliźniaczych reprezentacji można wziąć pod uwagę następujące czynniki:
* **Cennik**: Aby uzyskać szczegółowe informacje o cenach, zobacz  [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link). 
* **Dostępność regionalna**: w przypadku usługi Azure Digital bliźniaczych reprezentacji ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, w których jest dostępna usługa Azure Digital bliźniaczych reprezentacji. 
* **Maksymalna liczba prywatnych punktów końcowych na wystąpienie usługi Azure Digital bliźniaczych reprezentacji**: 10

Aby uzyskać informacje na temat limitów linku prywatnego, zobacz [Dokumentacja prywatnego linku platformy Azure: ograniczenia](../private-link/private-link-service-overview.md#limitations).

## <a name="service-tags"></a>Tagi usługi

**Tag usługi** reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych. Aby uzyskać więcej informacji na temat tagów usługi, zobacz  [*Tagi sieci wirtualnej*](../virtual-network/service-tags-overview.md). 

Możesz użyć tagów usługi do definiowania kontroli dostępu do sieci dla [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md#security-rules)   lub [zapory platformy Azure](../firewall/service-tags.md)przy użyciu tagów usługi zamiast określonych adresów IP podczas tworzenia reguł zabezpieczeń. Określając nazwę tagu usługi (w tym przypadku **AzureDigitalTwins**) w odpowiednim polu *źródłowym*   lub *docelowym*   reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. 

Poniżej znajdują się szczegółowe informacje dotyczące tagu usługi **AzureDigitalTwins** .

| Tag | Przeznaczenie | Może korzystać z ruchu przychodzącego lub wychodzącego? | Może być regionalna? | Czy można używać z zaporą platformy Azure? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>Uwaga: ten tag lub adresy IP objęte tym tagiem mogą służyć do ograniczania dostępu do punktów końcowych skonfigurowanych dla [tras zdarzeń](concepts-route-events.md). | Przychodzący | Nie | Tak |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Używanie tagów usługi do uzyskiwania dostępu do punktów końcowych trasy zdarzeń 

Poniżej przedstawiono procedurę uzyskiwania dostępu do punktów końcowych [trasy zdarzeń](concepts-route-events.md) przy użyciu tagów usługi w usłudze Azure Digital bliźniaczych reprezentacji.

1. Najpierw pobierz ten plik JSON zawierający zakresy i znaczniki usługi platformy Azure: [*zakresy adresów IP platformy Azure i Tagi usług*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. Wyszukaj adresy IP "AzureDigitalTwins" w pliku JSON.  

3. Zapoznaj się z dokumentacją zewnętrznego zasobu połączonego z punktem końcowym (na przykład [Event Grid](../event-grid/overview.md), [centrum zdarzeń](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)lub [Azure Storage](../storage/blobs/storage-blobs-overview.md) dla [zdarzeń utraconych](concepts-route-events.md#dead-letter-events)), aby zobaczyć, jak ustawić filtry IP dla tego zasobu.

4. Ustaw filtry IP dla zasobów zewnętrznych przy użyciu zakresów adresów IP z *kroku 2*.  

5. W razie potrzeby zaktualizuj zakresy adresów IP. Zakresy mogą ulec zmianie z upływem czasu, dlatego dobrym pomysłem jest jego regularne sprawdzenie i odświeżanie ich w razie potrzeby. Częstotliwość tych aktualizacji może się różnić, ale dobrym pomysłem jest sprawdzenie ich raz w tygodniu.

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie danych magazynowanych

Usługa Azure Digital bliźniaczych reprezentacji zapewnia szyfrowanie danych przechowywanych i przesyłanych w czasie, gdy jest ona zapisywana w centrach danych i odszyfrowuje ją, gdy uzyskujesz do niej dostęp. To szyfrowanie odbywa się przy użyciu klucza szyfrowania zarządzanego przez firmę Microsoft.

## <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

Usługa Azure Digital bliźniaczych reprezentacji obecnie nie obsługuje **udostępniania zasobów między źródłami (CORS)**. W związku z tym, jeśli wywołujesz interfejs API REST z aplikacji przeglądarki, interfejsu [API Management (APIM)](../api-management/api-management-key-concepts.md) lub łącznika [aplikacji zaawansowanych](/powerapps/powerapps-overview) , może zostać wyświetlony błąd zasad.

Aby rozwiązać ten problem, można wykonać jedną z następujących czynności:
* Przeplataj nagłówek CORS `Access-Control-Allow-Origin` z wiadomości. Ten nagłówek wskazuje, czy można udostępnić odpowiedź. 
* Alternatywnie można utworzyć serwer proxy CORS i wprowadzić żądanie interfejsu API REST usługi Azure Digital bliźniaczych reprezentacji. 

## <a name="next-steps"></a>Następne kroki

* Zobacz te koncepcje w akcji w czynnościach opisanych w temacie [*: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-portal.md).

* Zapoznaj się z tematem jak korzystać z tych koncepcji z kodu aplikacji klienta w artykule [*instrukcje: pisanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md).

* Przeczytaj więcej na temat kontroli [RBAC platformy Azure](../role-based-access-control/overview.md).