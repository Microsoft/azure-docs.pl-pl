---
title: Atrybuty cienia usługi synchronizacji Azure AD Connect | Microsoft Docs
description: Opisuje sposób działania atrybutów cieniowania w usłudze synchronizacji Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 128303cb51b39db8442fdda71f949db17923bfa2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90088974"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atrybuty cienia usługi synchronizacji Azure AD Connect
Większość atrybutów jest reprezentowana w ten sam sposób w usłudze Azure AD, ponieważ znajdują się one w Active Directory lokalnej. Ale niektóre atrybuty mają specjalne traktowanie i wartość atrybutu w usłudze Azure AD może różnić się od tego, co Azure AD Connect synchronizacja.

## <a name="introducing-shadow-attributes"></a>Wprowadzenie do atrybutów cienia
Niektóre atrybuty mają dwie reprezentacje w usłudze Azure AD. Są przechowywane zarówno wartość lokalna, jak i obliczona wartość. Te dodatkowe atrybuty są nazywane atrybutami cienia. Dwa najczęstsze atrybuty, w których widzisz to zachowanie, to **userPrincipalName** i **ProxyAddress**. Zmiana wartości atrybutów ma miejsce, gdy w tych atrybutach są podane wartości reprezentujące niezweryfikowane domeny. Mimo że aparat synchronizacji w programie Connect odczytuje wartość w atrybucie Shadow, więc z perspektywy, atrybut został potwierdzony przez usługę Azure AD.

Nie można wyświetlić atrybutów cienia przy użyciu Azure Portal lub programu PowerShell. Jednak zrozumienie koncepcji pomaga w rozwiązywaniu problemów z niektórymi scenariuszami, w których atrybut ma różne wartości w środowisku lokalnym i w chmurze.

Aby lepiej zrozumieć zachowanie, zapoznaj się z tym przykładem firmy Fabrikam:  
![Zrzut ekranu przedstawia Active Directory sufiks nazwy UPN dla kilku przykładów z odpowiadającą jej wartością domeny usługi Azure AD dodaną, niezweryfikowaną i zweryfikowaną.](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Mają one wiele sufiksów nazw UPN w Active Directory lokalnych, ale tylko je zweryfikowano.

### <a name="userprincipalname"></a>userPrincipalName
Użytkownik ma następujące wartości atrybutów w niezweryfikowanej domenie:

| Atrybut | Wartość |
| --- | --- |
| lokalny element userPrincipalName | lee.sperry@fabrikam.com |
| Usługa Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Element userPrincipalName usługi Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

Atrybut userPrincipalName jest wartością wyświetlaną podczas korzystania z programu PowerShell.

Ponieważ rzeczywista wartość atrybutu lokalnego jest przechowywana w usłudze Azure AD, podczas weryfikowania domeny fabrikam.com usługa Azure AD aktualizuje atrybut userPrincipalName wartością z shadowUserPrincipalName. Nie trzeba synchronizować żadnych zmian z Azure AD Connect, aby te wartości były aktualizowane.

### <a name="proxyaddresses"></a>proxyAddresses
Ten sam proces tylko w przypadku, gdy zweryfikowane domeny występuje również dla proxyAddresses, ale z dodatkową logiką. Sprawdzenie dla zweryfikowanych domen ma miejsce tylko w przypadku użytkowników skrzynek pocztowych. Użytkownik lub kontakt z włączoną obsługą poczty reprezentuje użytkownika w innej organizacji programu Exchange i można dodać do tych obiektów dowolne wartości z proxyAddresses.

W przypadku użytkownika skrzynki pocztowej lokalnego lub w usłudze Exchange Online pojawiają się tylko wartości dla zweryfikowanych domen. Może wyglądać następująco:

| Atrybut | Wartość |
| --- | --- |
| lokalne proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Usługa Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

W tym przypadku **SMTP: Abbie. spencer \@ fabrikam.com** został usunięty od czasu, gdy ta domena nie została zweryfikowana. Jednak Exchange dodano również **SIP: Abbie. spencer \@ fabrikamonline.com**. Firma Fabrikam nie użyła programu Lync/Skype lokalnie, ale usługa Azure AD i usługi Exchange Online są dla niego przygotowywane.

Ta logika dla proxyAddresses jest określana jako **ProxyCalc**. ProxyCalc jest wywoływana z każdą zmianą na użytkowniku, gdy:

- Użytkownikowi przypisano plan usługi, który obejmuje usługę Exchange Online, nawet jeśli użytkownik nie ma licencji na program Exchange. Jeśli na przykład użytkownik ma przypisaną jednostkę SKU pakietu Office E3, ale tylko przypisano do usługi SharePoint Online. Jest to prawdziwe, nawet jeśli skrzynka pocztowa jest nadal w środowisku lokalnym.
- Atrybut msExchRecipientTypeDetails ma wartość.
- Wprowadzisz zmianę do proxyAddresses lub userPrincipalName.

Przetwarzanie zmiany przez ProxyCalc może zająć trochę czasu i nie jest ona synchroniczna z procesem eksportowania Azure AD Connect.

> [!NOTE]
> Logika ProxyCalc zawiera dodatkowe zachowania dla zaawansowanych scenariuszy, które nie są udokumentowane w tym temacie. Ten temat jest przeznaczony do zrozumienia zachowania i nie dokumentu całej logiki wewnętrznej.

### <a name="quarantined-attribute-values"></a>Wartości atrybutów poddane kwarantannie
Atrybuty cienia są również używane, gdy istnieją zduplikowane wartości atrybutów. Aby uzyskać więcej informacji, zobacz [odporność na zduplikowane atrybuty](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Zobacz też
* [Synchronizacja Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).
