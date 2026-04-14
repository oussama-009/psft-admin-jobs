flowchart TD
    A[START] --> B[classifier]
    B --> C[recuperer_contexte]

    C --> D{router_mode}

    %% ===== GENERAL =====
    D -->|general| E[repondre_general]
    E --> Z[END]

    %% ===== DIAGNOSTIC =====
    D -->|diagnostic| F[analyser_question]
    F --> G{router_sources}

    %% Sources branches
    G -->|influxdb_seulement| H[chercher_influxdb]
    G -->|influxdb_et_loki| I[chercher_influxdb]
    G -->|loki_seulement| J[chercher_loki]

    %% After influx
    H --> K{router_apres_influxdb}
    I --> L{router_apres_influxdb}

    %% Routing after influx
    K -->|vers_correlation| M[corréler_analyser]
    L -->|vers_loki| N[chercher_loki]

    %% Loki path joins
    N --> M
    J --> M

    %% Analysis loop
    M --> O[decider_continuer]
    O --> P{router_continuer}

    %% End case
    P -->|terminer| Q[generer_reponse]
    Q --> Z

    %% Loop cases
    P -->|reboucle_influxdb| R[chercher_influxdb]
    P -->|reboucle_loki| S[chercher_loki]
    P -->|reboucle_influxdb_et_loki| T[chercher_influxdb + chercher_loki]

    %% Loop back
    R --> M
    S --> M
    T --> M
