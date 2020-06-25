---
title: 'Azure AD Connect: Rozwiązywanie problemów z kotwicą źródła podczas instalacji | Microsoft Docs'
description: Ten temat zawiera instrukcje dotyczące rozwiązywania problemów z kotwicą źródła podczas instalacji.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: cb82eb2d8e23daec0ddb8856b713c1aa051f25c5
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355951"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Rozwiązywanie problemów z kotwicą źródła podczas instalacji
W tym artykule wyjaśniono różne problemy związane z kotwicą źródłową, które mogą wystąpić podczas instalacji i przedstawiono sposoby rozwiązania tych problemów.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Nieprawidłowa kotwica źródła w Azure Active Directory

### <a name="custom-installation"></a>Instalacja niestandardowa

Podczas instalacji niestandardowej Azure AD Connect odczytuje źródłowe zasady kotwic z Azure Active Directory. Jeśli zasady istnieją w Azure Active Directory, Azure AD Connect stosuje te same zasady, chyba że zostaną zastąpione przez klienta. Kreator informuje, który atrybut został odczytany. Ponadto Kreator ostrzega w przypadku próby zastąpienia źródłowych zasad kotwicy.

Podczas tej operacji odczytu możliwe jest, że źródłowe zasady zakotwiczenia w Azure Active Directory są nieoczekiwane. W tym przypadku Azure AD Connect nie wie, do czego służy zakotwiczenie źródłowe i wymaga ręcznej przesłonięcia.</br>
![oczekiwan](media/tshoot-connect-source-anchor/source1.png)

Aby rozwiązać ten problem, można ręcznie przesłonić kotwicę źródłową, wybierając konkretny atrybut. Kontynuuj tę opcję, jeśli i tylko wtedy, gdy masz pewne atrybuty do wybrania. Jeśli nie masz pewności, skontaktuj się z działem [pomocy technicznej firmy Microsoft](https://support.microsoft.com/contactus/) w celu uzyskania wskazówek. Zmiana zasad zakotwiczenia źródła może spowodować przerwanie skojarzenia między użytkownikami lokalnymi i skojarzonymi z nimi zasobami platformy Azure.</br>
![oczekiwan](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Instalacja ekspresowa
Podczas instalacji ekspresowej Azure AD Connect odczytuje źródłowe zasady kotwic z Azure Active Directory. Jeśli zasady istnieją w Azure Active Directory, Azure AD Connect stosuje te same zasady. Nie ma możliwości przesłonięcia ręcznego.

Podczas tej operacji odczytu możliwe jest, że źródłowe zasady zakotwiczenia w Azure Active Directory są nieoczekiwane. W tym przypadku Azure AD Connect nie wie, jak powinna być zakotwiczenie źródłowe.</br>
![oczekiwan](media/tshoot-connect-source-anchor/source3.png)

Aby rozwiązać ten problem, należy ponownie zainstalować program przy użyciu trybu niestandardowego i ręcznie przesłonić zakotwiczenie źródła przez wybranie określonego atrybutu. Kontynuuj tę opcję, jeśli i tylko wtedy, gdy masz pewne atrybuty do wybrania. Jeśli nie masz pewności, skontaktuj się z działem [pomocy technicznej firmy Microsoft](https://support.microsoft.com/contactus/) w celu uzyskania wskazówek. Zmiana zasad zakotwiczenia źródła może spowodować przerwanie skojarzenia między użytkownikami lokalnymi i skojarzonymi z nimi zasobami platformy Azure.

### <a name="invalid-source-anchor-in-sync-engine"></a>Nieprawidłowe zakotwiczenie źródła w aparacie synchronizacji
Podczas instalacji możliwe jest Azure AD Connect prób skonfigurowania aparatu synchronizacji przy użyciu nieprawidłowej kotwicy źródła. Ta operacja jest najbardziej prawdopodobną przyczyną problemu z produktem, a instalacja Azure AD Connect zakończy się niepowodzeniem. Skontaktuj się z [pomocą techniczną firmy Microsoft](https://support.microsoft.com/contactus/) , jeśli korzystasz z tego problemu.</br>
![oczekiwan](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
