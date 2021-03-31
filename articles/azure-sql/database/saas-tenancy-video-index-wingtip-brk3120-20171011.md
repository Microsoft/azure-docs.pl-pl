---
title: Film wideo aplikacji SQL SaaS
description: Ten artykuł indeksuje różne punkty czasu w naszym wideo 81 minut na temat projektu aplikacji SaaS DB — od konferencji zapłonowej w dniu 11 października 2017. Możesz przejść do części, która Cię interesuje. Opisano co najmniej 3 wzorce. Funkcje platformy Azure, które upraszczają programowanie i zarządzanie, zostały opisane poniżej.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.custom: sqldbrb=1
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 8cceeef7e41e8fc5c48b4e67cdb1adf3aa4f02a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89483868"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Wideo indeksowane i adnotacje dla wielodostępnej aplikacji SaaS przy użyciu Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten artykuł jest indeksem z adnotacjami w lokalizacji czasowych wideo 81 minut na temat modeli dzierżawy SaaS lub wzorców. Ten artykuł umożliwia przechodzenie do tyłu lub do przodu w klipie wideo, do którego jest interesująca dana część. W filmie wideo objaśniono główne opcje projektowania aplikacji bazy danych z wieloma dzierżawcami na Azure SQL Database. Film wideo zawiera pokazy, przewodniki związane z kodem zarządzania, a także bardziej szczegółowe informacje o doświadczeniu, które nie mogą znajdować się w naszej dokumentacji.

Film wideo wzmacnia informacje w naszej dokumentacji, która została znaleziona w: 
- *Koncepcyjne:* [wzorce dzierżawy bazy danych SaaS z wieloma dzierżawcami][saas-concept-design-patterns-563e]
- *Samouczki:* [aplikacja SaaS bilety Wingtip][saas-how-welcome-wingtip-app-679t]

Film wideo i artykuły opisują wiele faz tworzenia aplikacji wielodostępnych na Azure SQL Database w chmurze. Specjalne funkcje Azure SQL Database ułatwiają tworzenie i implementowanie aplikacji z wieloma dzierżawcami, które są łatwiejsze do zarządzania i niezawodnego wykonywania.

Rutynowo aktualizujemy zapisaną dokumentację. Film wideo nie jest edytowany ani aktualizowany, a ostatecznie więcej jego szczegółów może stać się nieaktualna.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sekwencja 38ych zrzutów ekranu czasowego

Ta sekcja służy do indeksowania lokalizacji czasu dla dyskusji 38 w ciągu około 81 minut wideo. Każdy indeks czasu jest adnotacją z zrzutem ekranu z filmu wideo, a czasami z dodatkowymi informacjami.

Każdy indeks czasu ma format *h:mm: SS*. Na przykład druga indeksowana lokalizacja, do której **zamierzenia sesji**, jest uruchamiana w przybliżonej lokalizacji czasu **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Kompaktowanie linków do indeksowanych lokalizacji wideo

Poniższe tytuły są łączami do odpowiednich sekcji z adnotacjami w dalszej części tego artykułu:

- [1. **(Rozpocznij)** slajd powitalny, 0:00:03](#anchor-image-wtip-min00001)
- [2. cele sesji, 0:03:11](#anchor-image-wtip-min00311)
- [3. Agenda, 0:04:17](#anchor-image-wtip-min00417)
- [4. wielodostępna aplikacja sieci Web, 0:05:05](#anchor-image-wtip-min00505)
- [5. formularz sieci Web aplikacji w działaniu, 0:05:55](#anchor-image-wtip-min00555)
- [6. koszt za dzierżawcę (skala, izolacja, odzyskiwanie), 0:09:31](#anchor-image-wtip-min00931)
- [7. modele baz danych dla wielu dzierżawców: zalety i wady, 0:11:59](#anchor-image-wtip-min01159)
- [8. model hybrydowy miesza korzyści z używania MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. POJEDYNCZA dzierżawa a wiele dzierżawców: zalety i wady, 0:16:44](#anchor-image-wtip-min01644)
- [10. pule są ekonomiczne dla nieprzewidywalnych obciążeń, 0:19:36](#anchor-image-wtip-min01936)
- [11. Demonstracja bazy danych na dzierżawcę i w postaci hybrydowej ST/MT 0:20:08](#anchor-image-wtip-min02008)
- [12. Live formularz aplikacji z Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. firma MYOB, a nie DBA o szczegółowe informacje, 0:28:54](#anchor-image-wtip-min02854)
- [14. firma MYOB użycie puli elastycznej — przykład 0:29:40](#anchor-image-wtip-min02940)
- [15. uczenie się od firma MYOB i innych niezależnych dostawców oprogramowania, 0:31:36](#anchor-image-wtip-min03136)
- [16. wzorce redagują do scenariusza E2E SaaS, 0:43:15](#anchor-image-wtip-min04315)
- [17. kanoniczna hybrydowa wielodostępna aplikacja SaaS, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS — Przykładowa aplikacja, 0:48:10](#anchor-image-wtip-min04810)
- [19. scenariusze i wzorce omówione w samouczkach, 0:49:10](#anchor-image-wtip-min04910)
- [20. Demonstracja samouczków i repozytorium GitHub, 0:50:18](#anchor-image-wtip-min05018)
- [21. repozytorium GitHub Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Eksplorowanie wzorców, 0:56:20](#anchor-image-wtip-min05620)
- [23. Inicjowanie obsługi dzierżaw i dołączanie, 0:57:44](#anchor-image-wtip-min05744)
- [24. Inicjowanie obsługi dzierżaw i połączenia aplikacji, 0:58:58](#anchor-image-wtip-min05858)
- [25. Demonstracja skryptów zarządzania Inicjowanie obsługi pojedynczej dzierżawy, 0:59:43](#anchor-image-wtip-min05943)
- [26. program PowerShell do udostępniania i katalogowania, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Zarządzanie nieprzewidywalnymi obciążeniami dzierżawców, 1:04:36](#anchor-image-wtip-min10436)
- [29. elastyczne monitorowanie puli, 1:06:39](#anchor-image-wtip-min10639)
- [30. generowanie obciążenia i monitorowanie wydajności, 1:09:42](#anchor-image-wtip-min10942)
- [31. Zarządzanie schematami na dużą skalę, 1:10:33](#anchor-image-wtip-min11033)
- [32. zapytanie rozproszone w bazach danych dzierżaw, 1:12:21](#anchor-image-wtip-min11221)
- [33. Demonstracja generowania biletu, 1:12:32](#anchor-image-wtip-min11232)
- [34. Analiza ad hoc, 1:12:46](#anchor-image-wtip-min11246)
- [35. Wyodrębnij dane dzierżawy do usługi Azure Synapse Analytics, 1:16:32](#anchor-image-wtip-min11632)
- [36. Graf dziennej dystrybucji sprzedaży, 1:16:48](#anchor-image-wtip-min11648)
- [37. Zapakuj i Wywołaj akcję, 1:19:52](#anchor-image-wtip-min11952)
- [38. zasoby Aby uzyskać więcej informacji, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Indeksowane adnotacje lokalizacje czasowe w wideo

Kliknięcie dowolnego obrazu zrzutu ekranu spowoduje przejście do dokładnej lokalizacji w filmie wideo.


&nbsp; <a name="anchor-image-wtip-min00001"></a>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Rozpocznij)* slajd powitalny, 0:00:01

*Uczenie się z firma MYOB: wzorce projektowe dla aplikacji SaaS na Azure SQL Database-BRK3120*

[![Slajd powitalny][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Title: uczenie się od firma MYOB: projektowanie wzorców dla aplikacji SaaS na Azure SQL Database
- Bill.Gibson@microsoft.com
- Główny Menedżer programu, Azure SQL Database
- BRK3120 z zapłonem firmy Microsoft, Orlando, FL USA, Październik/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"></a>
#### <a name="2-session-objectives-00153"></a>2. cele sesji, 0:01:53
[![Cele sesji][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternatywne modele dla aplikacji wielodostępnych, z zaletami i wadami.
- SaaS wzorce, aby zmniejszyć koszty opracowywania, zarządzania i zasobów.
- Przykładowa aplikacja i skrypty.
- PaaS funkcje i wzorce SaaS sprawiają, że SQL Database wysoce skalowalną, ekonomiczną platformą danych dla wielu dzierżawców SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"></a>
#### <a name="3-agenda-00409"></a>3. Agenda, 0:04:09
[![Punktu][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"></a>
#### <a name="4-multi-tenant-web-app-00500"></a>4. wielodostępna aplikacja sieci Web, 0:05:00
[![Aplikacja Wingtip SaaS: wielodostępna aplikacja sieci Web][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"></a>
#### <a name="5-app-web-form-in-action-00539"></a>5. formularz sieci Web aplikacji w działaniu, 0:05:39
[![Formularz sieci Web aplikacji w działaniu][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"></a>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. koszt za dzierżawcę (skala, izolacja, odzyskiwanie), 0:06:58
[![Koszt dla dzierżawy, skala, izolacja, odzyskiwanie][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"></a>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. modele baz danych dla wielu dzierżawców: zalety i wady, 0:09:52
[![Modele baz danych dla wielu dzierżawców: zalety i wady][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"></a>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. model hybrydowy miesza korzyści z używania MT/ST, 0:12:29
[![Model hybrydowy miesza korzyści z używania MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"></a>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. POJEDYNCZA dzierżawa a wiele dzierżawców: zalety i wady, 0:13:11
[![Pojedyncza dzierżawa a wiele dzierżawców: zalety i wady][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"></a>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. pule są ekonomiczne dla nieprzewidywalnych obciążeń, 0:17:49
[![Pule są ekonomiczne dla nieprzewidywalnych obciążeń][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"></a>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Demonstracja bazy danych na dzierżawcę i w postaci hybrydowej ST/MT 0:19:59
[![Demonstracja bazy danych na dzierżawcę i w trybie hybrydowym (ST/MT)][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"></a>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Live formularz aplikacji z Dojo, 0:20:10
[![Formularz usługi Live App pokazujący Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"></a>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. firma MYOB, a nie DBA o szczegółowe informacje, 0:25:06
[![Firma MYOB i nie DBA o szczegółowe informacje][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"></a>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. firma MYOB użycie puli elastycznej — przykład 0:29:30
[![Przykład użycia puli elastycznej firma MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"></a>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. uczenie się od firma MYOB i innych niezależnych dostawców oprogramowania, 0:31:25
[![Uczenie się od firma MYOB i innych niezależnych dostawców oprogramowania][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"></a>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. wzorce redagują do scenariusza E2E SaaS, 0:31:42
[![Wzorce tworzą w scenariuszu E2E SaaS][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"></a>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. kanoniczna hybrydowa wielodostępna aplikacja SaaS, 0:46:04
[![Kanoniczna hybrydowa aplikacja wielodostępna SaaS][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"></a>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS — Przykładowa aplikacja, 0:48:01
[![Przykładowa aplikacja Wingtip SaaS][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"></a>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. scenariusze i wzorce omówione w samouczkach, 0:49:00
[![Scenariusze i wzorce omówione w samouczkach][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"></a>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Demonstracja samouczków i repozytorium GitHub, 0:50:12
[![Samouczki demonstracyjne i repozytorium GitHub][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"></a>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. repozytorium GitHub Microsoft/WingtipSaaS, 0:50:32
[![Repozytorium GitHub Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"></a>
#### <a name="22-exploring-the-patterns-05615"></a>22. Eksplorowanie wzorców, 0:56:15
[![Eksplorowanie wzorców][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"></a>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Inicjowanie obsługi dzierżaw i dołączanie, 0:56:19
[![Inicjowanie obsługi dzierżaw i dołączanie][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"></a>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Inicjowanie obsługi dzierżaw i połączenia aplikacji, 0:57:52
[![Inicjowanie obsługi dzierżaw i połączenia aplikacji][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"></a>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Demonstracja skryptów zarządzania Inicjowanie obsługi pojedynczej dzierżawy, 0:59:36
[![Demonstracja skryptów zarządzania aprowizacji pojedynczej dzierżawy][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"></a>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. program PowerShell do udostępniania i katalogowania, 0:59:56
[![Program PowerShell do aprowizacji i katalogowania][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"></a>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![Wybierz pozycję T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"></a>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Zarządzanie nieprzewidywalnymi obciążeniami dzierżawców, 1:03:34
[![Zarządzanie nieprzewidywalnymi obciążeniami dzierżawy][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"></a>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. elastyczne monitorowanie puli, 1:06:32
[![Monitorowanie puli elastycznej][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"></a>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. generowanie obciążenia i monitorowanie wydajności, 1:09:37
[![Generowanie obciążenia i monitorowanie wydajności][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"></a>
#### <a name="31-schema-management-at-scale-10940"></a>31. Zarządzanie schematami na dużą skalę, 1:09:40
[![Zarządzanie schematami na dużą skalę][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"></a>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. zapytanie rozproszone w bazach danych dzierżaw, 1:11:18
[![Zapytania rozproszone w bazach danych dzierżaw][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"></a>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Demonstracja generowania biletu, 1:12:28
[![Demonstracja generowania biletu][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"></a>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. Analiza ad hoc, 1:12:35
[![Analiza ad hoc][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"></a>
#### <a name="35-extract-tenant-data-into-azure-synapse-analytics-11546"></a>35. Wyodrębnij dane dzierżawy do usługi Azure Synapse Analytics, 1:15:46
[![Wyodrębnij dane dzierżawy do usługi Azure Synapse Analytics][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"></a>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Graf dziennej dystrybucji sprzedaży, 1:16:38
[![Graf dziennej dystrybucji sprzedaży][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"></a>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Zapakuj i Wywołaj akcję, 1:17:43
[![Zawijaj i Wywołaj akcję][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"></a>
#### <a name="38-resources-for-more-information-12035"></a>38. zasoby Aby uzyskać więcej informacji, 1:20:35
[![Zasoby, aby uzyskać więcej informacji][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Wpis w blogu, 22 maja 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Koncepcyjne:* [wzorce dzierżawy bazy danych SaaS z wieloma dzierżawcami][saas-concept-design-patterns-563e]

- *Samouczki:* [aplikacja SaaS bilety Wingtip][saas-how-welcome-wingtip-app-679t]

- Repozytoria GitHub dla wersji Wingtip biletów SaaS aplikacji dzierżawców:
    - [Repozytorium GitHub dla modelu aplikacji autonomicznej][github-wingtip-standaloneapp].
    - [Repozytorium GitHub dla bazy danych na model dzierżawców][github-wingtip-dbpertenant].
    - [Repozytorium GitHub — model bazy danych z wieloma dzierżawcami][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Następne kroki

- [Pierwszy samouczek][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Slajd powitalny"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Cele sesji."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Punktu."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Aplikacja Wingtip SaaS: wielodostępna aplikacja sieci Web"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Formularz sieci Web aplikacji w działaniu"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Koszt dla dzierżawy, skala, izolacja, odzyskiwanie"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Modele baz danych dla wielu dzierżawców: zalety i wady"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Model hybrydowy miesza korzyści z używania MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Pojedyncza dzierżawa a wiele dzierżawców: zalety i wady"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Pule są ekonomiczne dla nieprzewidywalnych obciążeń"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demonstracja bazy danych na dzierżawcę i w trybie hybrydowym (ST/MT)"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Formularz usługi Live App pokazujący Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "Firma MYOB i nie DBA o szczegółowe informacje"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Przykład użycia puli elastycznej firma MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Uczenie się od firma MYOB i innych niezależnych dostawców oprogramowania"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Wzorce tworzą w scenariuszu E2E SaaS"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Kanoniczna hybrydowa aplikacja wielodostępna SaaS"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Przykładowa aplikacja Wingtip SaaS"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scenariusze i wzorce omówione w samouczkach"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Demonstracja samouczków i repozytorium GitHub"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Repozytorium GitHub Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Eksplorowanie wzorców"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Inicjowanie obsługi dzierżaw i dołączanie"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Inicjowanie obsługi dzierżaw i połączenia aplikacji"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Demonstracja skryptów zarządzania aprowizacji pojedynczej dzierżawy"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "Program PowerShell do aprowizacji i katalogowania"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "Wybierz pozycję T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Zarządzanie nieprzewidywalnymi obciążeniami dzierżawy"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Monitorowanie puli elastycznej"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Generowanie obciążenia i monitorowanie wydajności"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Zarządzanie schematami na dużą skalę"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Zapytania rozproszone w bazach danych dzierżaw"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demonstracja generowania biletu"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Analiza ad hoc"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Wyodrębnij dane dzierżawy do usługi Azure Synapse Analytics"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Graf dziennej dystrybucji sprzedaży"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Zawijaj i Wywołaj akcję"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Zasoby, aby uzyskać więcej informacji"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

