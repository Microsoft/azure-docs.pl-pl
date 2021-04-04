---
title: Typy zasobów rozszerzenia
description: Wyświetla listę typów zasobów platformy Azure służących do rozszerania możliwości innych typów zasobów.
ms.topic: conceptual
ms.date: 11/14/2020
ms.openlocfilehash: 5561c480dd5a2849588ed2288eb5bcc35fc1446c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94658455"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Typy zasobów, które zwiększają możliwości innych zasobów

Zasób rozszerzenia jest zasobem, który dodaje do możliwości innego zasobu. Na przykład blokada zasobu jest zasobem rozszerzenia. Należy zastosować blokadę zasobu do innego zasobu, aby zapobiec jego usunięciu lub zmodyfikowaniu. Nie ma sensu, aby utworzyć tylko blokadę zasobu. Zasób rozszerzenia jest zawsze stosowany do innego zasobu.

## <a name="microsoftadvisor"></a>Microsoft. Advisor

- Microsoft. Advisor/konfiguracje
- Microsoft. Advisor/zalecenia
- Microsoft. Advisor/tłumienie

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

- Microsoft. AlertsManagement/alerty

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/Locks
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions

## <a name="microsoftautomanage"></a>Microsoft. Autozarządzanie

- Microsoft. Autozarządzanie/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft. rozliczenia

- Microsoft. rozliczenia/billingPeriods
- Microsoft. rozliczenia/billingPermissions
- Microsoft. rozliczenia/billingRoleAssignments
- Microsoft. rozliczenia/billingRoleDefinitions
- Microsoft. rozliczenia/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft. plan

- Microsoft. plan/blueprintAssignments
- Microsoft. plan/plany

## <a name="microsoftconsumption"></a>Microsoft. zużycie

- Microsoft. zużycie/AggregatedCost
- Microsoft. zużycie/salda
- Microsoft. zużycie/budżety
- Microsoft. zużycie/opłaty
- Microsoft. zużycie/CostTags
- Microsoft. zużycie/kredyty
- Microsoft. użycie/zdarzenia
- Microsoft. zużycie/prognozy
- Microsoft. zużycie/partie
- Microsoft. zużycie/Marketplace
- Microsoft. zużycie/Pricesheets
- Microsoft. zużycie/produkty
- Microsoft. zużycie/ReservationDetails
- Microsoft. zużycie/ReservationRecommendationDetails
- Microsoft. zużycie/ReservationRecommendations
- Microsoft. zużycie/ReservationSummaries
- Microsoft. zużycie/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

- Microsoft. ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

- Microsoft. CostManagement/alerty
- Microsoft. CostManagement/budżety
- Microsoft. CostManagement/wymiary
- Microsoft. CostManagement/eksporty
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/Prognoza
- Microsoft. CostManagement/Insights
- Microsoft. CostManagement/Query
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/raporty
- Microsoft. CostManagement/widoki

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

- Microsoft. CustomProviders/skojarzenia

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/oprogramowanie

## <a name="microsoftinsights"></a>Microsoft. Insights

- Microsoft. Insights/Baseline
- Microsoft. Insights/dataCollectionRuleAssociations
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/eventtypes
- Microsoft. Insights/extendedDiagnosticSettings
- Microsoft. Insights/guestDiagnosticSettingsAssociation
- Microsoft. Insights/logDefinitions
- Microsoft. Insights/Logs
- Microsoft. Insights/metricbaselines
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/metricNamespaces
- Microsoft. Insights/metryki
- Microsoft. Insights/webskoroszyty
- Microsoft. Insights/topologia
- Microsoft. Insights/Transactions

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

- Microsoft. KubernetesConfiguration/rozszerzenia
- Microsoft. KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Konserwacja/aktualizacje

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

- Microsoft. ManagedIdentity/tożsamości

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

- Microsoft. OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

- Microsoft. OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

- Microsoft. PolicyInsights/zaświadczanie
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft. PolicyInsights/korygowania

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

- Microsoft. RecoveryServices/backupProtectedItems
- Microsoft. RecoveryServices/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/Events
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/powiadomienia

## <a name="microsoftresources"></a>Microsoft. resources

- Microsoft. resources/Links
- Microsoft. resources/Tags

## <a name="microsoftsecurity"></a>Microsoft.Security

- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/Assessments
- Microsoft. zabezpieczenia/zgodność
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/Devices
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/iotSensors
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

- Microsoft. SecurityInsights/agregacje
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/automationRules
- Microsoft. SecurityInsights/zakładki
- Microsoft. SecurityInsights/sprawy
- Microsoft. SecurityInsights/dataconnecters
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/jednostki
- Microsoft. SecurityInsights/incydenty
- Microsoft. SecurityInsights/ustawienia
- Microsoft. SecurityInsights/threatIntelligence
- Microsoft. SecurityInsights/watchlists

## <a name="microsoftserialconsoleppe"></a>Microsoft. SerialConsole. ŚOI

- Microsoft. SerialConsole. ŚOI/serialPorts

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

- Microsoft. SoftwarePlan/hybridUseBenefits

## <a name="microsoftsupport"></a>Microsoft. Support

- Microsoft. Support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft. Monitor obciążenia został

- Microsoft. Monitor obciążenia został/składniki
- Microsoft. Monitor obciążenia został/monitorInstances
- Microsoft. Monitor obciążenia został/monitory
- Microsoft. Monitor obciążenia został/notificationSettings

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać identyfikator zasobu dla zasobu rozszerzenia w szablonie Azure Resource Manager, użyj [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Aby zapoznać się z przykładem tworzenia zasobu rozszerzenia w szablonie, zobacz [Event Grid subskrypcje zdarzeń](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
