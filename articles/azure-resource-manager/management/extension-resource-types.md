---
title: Typy zasobów rozszerzenia
description: Wyświetla listę typów zasobów platformy Azure służących do rozszerania możliwości innych typów zasobów.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3a3fbc531750bec4b16e38f1fe79f613c1b94f5e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80754875"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Typy zasobów, które zwiększają możliwości innych zasobów

Zasób rozszerzenia jest zasobem, który dodaje do możliwości innego zasobu. Na przykład blokada zasobu jest zasobem rozszerzenia. Należy zastosować blokadę zasobu do innego zasobu, aby zapobiec jego usunięciu lub zmodyfikowaniu. Nie ma sensu, aby utworzyć tylko blokadę zasobu. Zasób rozszerzenia jest zawsze stosowany do innego zasobu.

## <a name="extension-resource-types"></a>Typy zasobów rozszerzenia

- Microsoft. Advisor/konfiguracje
- Microsoft. Advisor/zalecenia
- Microsoft. Advisor/tłumienie
- Microsoft. AlertsManagement/alerty
- Microsoft. AlertsManagement/alertsSummary
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/findOrphanRoleAssignments
- Microsoft. Authorization/Locks
- Microsoft. Autoryzacja/uprawnienia
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleAssignmentsUsageMetrics
- Microsoft. Authorization/roleDefinitions
- Microsoft. rozliczenia/billingPeriods
- Microsoft. rozliczenia/billingPermissions
- Microsoft. rozliczenia/billingRoleAssignments
- Microsoft. rozliczenia/billingRoleDefinitions
- Microsoft. rozliczenia/createBillingRoleAssignment
- Microsoft. plan/blueprintAssignments
- Microsoft. plan/plany
- Microsoft. zużycie/AggregatedCost
- Microsoft. zużycie/salda
- Microsoft. zużycie/budżety
- Microsoft. zużycie/opłaty
- Microsoft. zużycie/CostTags
- Microsoft. zużycie/prognozy
- Microsoft. zużycie/Marketplace
- Microsoft. zużycie/OperationResults
- Microsoft. zużycie/OperationStatus
- Microsoft. zużycie/Pricesheets
- Microsoft. zużycie/ReservationDetails
- Microsoft. zużycie/ReservationRecommendations
- Microsoft. zużycie/ReservationSummaries
- Microsoft. zużycie/ReservationTransactions
- Microsoft. użycie/Tagi
- Microsoft. zużycie/warunki
- Microsoft. zużycie/UsageDetails
- Microsoft. zużycie/kredyty
- Microsoft. użycie/zdarzenia
- Microsoft. zużycie/partie
- Microsoft. zużycie/produkty
- Microsoft. użycie/dzierżawy
- Microsoft. ContainerInstance/serviceAssociationLinks
- Microsoft. CostManagement/alerty
- Microsoft. CostManagement/budżety
- Microsoft. CostManagement/wymiary
- Microsoft. CostManagement/eksporty
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/Prognoza
- Microsoft. CostManagement/Query
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/raporty
- Microsoft. CostManagement/widoki
- Microsoft. CostManagement/showbackRules
- Microsoft. CustomProviders/skojarzenia
- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics
- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/oprogramowanie
- Microsoft. GuestConfiguration/softwareUpdateProfile
- Microsoft. GuestConfiguration/softwareUpdates
- Microsoft. Insights/Baseline
- Microsoft. Insights/calculatebaseline
- Microsoft. Insights/dataCollectionRuleAssociations
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/eventtypes
- Microsoft. Insights/extendedDiagnosticSettings
- Microsoft. Insights/guestDiagnosticSettingsAssociation
- Microsoft. Insights/logDefinitions
- Microsoft. Insights/Logs
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/metricNamespaces
- Microsoft. Insights/metricbaselines
- Microsoft. Insights/metryki
- Microsoft. Insights/webskoroszyty
- Microsoft. Insights/topologia
- Microsoft. Insights/Transactions
- Microsoft. Insights/vmInsightsOnboardingStatuses
- Microsoft. KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Konserwacja/aktualizacje
- Microsoft. ManagedIdentity/tożsamości
- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions
- Microsoft. OperationalInsights/storageInsightConfigs
- Microsoft. OperationsManagement/managementassociations
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft. PolicyInsights/korygowania
- Microsoft. RecoveryServices/backupProtectedItems
- Microsoft. RecoveryServices/replicationEligibilityResults    
- Microsoft. ResourceHealth/availabilityStatuses
- Microsoft. ResourceHealth/childAvailabilityStatuses
- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/Events
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/powiadomienia
- Microsoft. resources/Links
- Microsoft. resources/Tags
- Microsoft. zabezpieczenia/zgodność
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/Assessments
- Microsoft. Security/complianceResults
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/networkData
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. SecurityInsights/agregacje
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/zakładki
- Microsoft. SecurityInsights/sprawy
- Microsoft. SecurityInsights/dataconnecters
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/jednostki
- Microsoft. SecurityInsights/entityQueries
- Microsoft. SecurityInsights/incydenty
- Microsoft. SecurityInsights/officeConsents
- Microsoft. SecurityInsights/ustawienia
- Microsoft. SoftwarePlan/hybridUseBenefits
- Microsoft. subskrypcja/anulowanie subskrypcji
- Microsoft. Support/supporttickets
- Microsoft. Monitor obciążenia został/składniki
- Microsoft. Monitor obciążenia został/monitorInstances
- Microsoft. Monitor obciążenia został/monitory
- Microsoft. Monitor obciążenia został/notificationSettings

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać identyfikator zasobu dla zasobu rozszerzenia w szablonie Azure Resource Manager, użyj [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Aby zapoznać się z przykładem tworzenia zasobu rozszerzenia w szablonie, zobacz [Event Grid subskrypcje zdarzeń](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
