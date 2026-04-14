```mermaid
graph TD
    classDef startEnd fill:#f96,stroke:#333,stroke-width:2px;
    classDef process fill:#bbf,stroke:#333,stroke-width:1px;
    classDef decision fill:#ddf,stroke:#333,stroke-width:1px;
    
    START([START]) ::: startEnd --> classifier[classifier]:::process
    
    classifier --> |"toujours appelé"| recuperer_contexte[recuperer_contexte]:::process
    recuperer_contexte --> router_mode{"router_mode"}:::decision
    
    router_mode -->|"general"| repondre_general[repondre_general]:::process
    repondre_general --> END1([END]) ::: startEnd
    
    router_mode -->|"diagnostic"| analyser_question[analyser_question]:::process
    
    analyser_question --> router_sources{"router_sources"}:::decision
    
    router_sources -->|"influxdb_seulement"| chercher_influxdb[chercher_influxdb]:::process
    router_sources -->|"influxdb_et_loki"| chercher_influxdb
    router_sources -->|"loki_seulement"| chercher_loki[chercher_loki]:::process
    
    chercher_influxdb --> router_apres_influxdb{"router_apres_influxdb"}:::decision
    
    router_apres_influxdb -->|"vers_correlation"| correler_analyser["corréler_analyser"]:::process
    router_apres_influxdb -->|"vers_loki"| chercher_loki
    
    chercher_loki --> correler_analyser
    
    correler_analyser --> decider_continuer[decider_continuer]:::process
    
    decider_continuer --> router_continuer{"router_continuer"}:::decision
    
    router_continuer -->|"terminer"| generer_reponse[generer_reponse]:::process
    generer_reponse --> END2([END]) ::: startEnd
    
    router_continuer -->|"reboucle_influxdb"| chercher_influxdb
    router_continuer -->|"reboucle_influxdb_et_loki"| chercher_influxdb
    router_continuer -->|"reboucle_loki"| chercher_loki
```
