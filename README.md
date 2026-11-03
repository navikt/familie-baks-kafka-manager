# Kafka Manager

[Kafka Manager](https://github.com/navikt/kafka-manager) er en applikasjon for å administrere Kafka-topics.
Du kan hente ut meldinger, se consumer-offsets og offset på siste melding, og sette consumer-offsets.

Appen er skrudd av som standard og deployes manuelt ved behov.

Du må ha forvalter-rollen for BAKS og være på naisdevice for å bruke appen. Se også [kafka-manager i familie-dokumentasjonen](https://github.com/navikt/familie/blob/main/doc/drift/kafka-manager.md), blant annet om å sette offset for en consumer group.

## Oppdatere versjonen

1. Åpne [Artifact Registry](https://console.cloud.google.com/artifacts/docker/nais-management-233d/europe-north1/poao/kafka-manager).
2. Finn nyeste publiserte image og kopier taggen.
3. Sett taggen som verdi for `kafkaManagerVersion` i [vars.yaml](.nais/vars.yaml). Dev og prod bruker samme fil.
4. Commit og push endringen til branchen du vil deploye fra.

## Legge til topic

1. Legg topicen til i `APP_CONFIG_JSON` i [dev](.nais/familie-baks-kafka-manager-dev.yaml) og/eller [prod](.nais/familie-baks-kafka-manager-prod.yaml).
2. Gi appen lesetilgang i repoet som eier topicen (for eksempel `.nais/kafka/` i familie-ba-sak eller familie-ks-sak). Legg til oppføringen i den eksisterende `spec.acl`-lista, både i dev- og prod-fila:

   ```yaml
   spec:
     acl:
       # ... eksisterende oppføringer
       - team: teamfamilie
         application: familie-baks-kafka-manager
         access: read
   ```

3. Deploy topic-endringen med workflowen **Deploy kafka topics** i repoet som eier topicen, og [deploy Kafka Manager](#deploye-manuelt) på nytt.

Uten denne tilgangen kan ikke Kafka Manager lese fra topicen.

## Deploye manuelt

Før du deployer, bør du [oppdatere til siste versjon](#oppdatere-versjonen) av Kafka Manager for å redusere risikoen for kjente sikkerhetshull.

1. Åpne **Actions** i GitHub og velg workflowen **Deploy familie-baks-kafka-manager**.
2. Trykk **Run workflow** og velg branchen med konfigurasjonen du vil deploye.
3. Huk av for **Deploy også til prod-gcp** hvis du også vil deploye til prod. Dev deployes alltid først.
4. Trykk **Run workflow** for å starte deployment.

Workflowen er definert i [deploy.yaml](.github/workflows/deploy.yaml).

## Etter bruk

Slett appen `familie-baks-kafka-manager` fra [Nais Console](https://console.nav.cloud.nais.io/team/teamfamilie/applications?filter=familie-baks-kafka-manager) i miljøene du deployet til, når du er ferdig med å bruke den.

## Miljøer

- Dev: https://familie-baks-kafka-manager.intern.dev.nav.no
- Prod: https://familie-baks-kafka-manager.intern.nav.no
