# Common Components

<div align="center">

```mermaid
graph TD;
    subgraph "Utility Score Management"
        UpdateUtilityScores["Update Point Utility Scores:
        1. Increment Evader Point Score
        2. Increment Pursuer Point Score
        Note: All point scores halved globally on time interval"]
    end
```

</div>

# Common Statistical Definitions

<div align="center">

```mermaid
graph TD;
    subgraph "Adaptive Statistical Threshold Check (ASTC)"
        direction TB
        Input[/"Input Value"/]
        Sensitivity[/"Sensitivity k"/]
        ThresholdCheck{"Value > μ + kσ?"}
        Formula["where:
        μ = EMA of all values
        σ = EMA(values > μ) - EMA(values < μ)"]
        
        ThresholdCheck --- Formula
        Yes[/"Yes"/]
        No[/"No"/]
        
        Input --> ThresholdCheck
        Sensitivity --> ThresholdCheck
        ThresholdCheck -->|Yes| Yes
        ThresholdCheck -->|No| No
    end
```

</div>

# Token Instance Flow

<div align="center">

```mermaid
graph LR;
    ExternalSources["External Token Sources"]

    subgraph "Token Instance Properties"
        direction TB
        Triggerable["Triggerable"]
        NonTriggerable["Non-Triggerable"]
        Suppressible["Suppressible"]
        NonSuppressible["Non-Suppressible"]
    end

    ExternalSources --> Triggerable & NonTriggerable
    Triggerable & NonTriggerable --> Suppressible & NonSuppressible

    Ingestion["Token Ingestion Point"]
    
    Suppressible --> Ingestion
    NonSuppressible --> Ingestion
```

</div>

# Token Instance Ingestion and Type Creation

<div align="center">

```mermaid
graph TD;
    ExternalSources["External Token Sources"]
    
    subgraph "Token Ingestion Point"
        TokenCheck{{"Token Type Exists?"}}
        UpdateProcess["Update Process"]
        CreateToken["Create New Token Type"]
        
        TokenCheck -->|Yes| UpdateProcess
        TokenCheck -->|No| CreateToken

        subgraph "Sphere Surface Points"
            SphereCreate["Random Point Generation"]
            NewPursuerPoint["Single Pursuer Point"]
            NewEvaderPoint["Single Evader Point"]
            
            CreateToken -->|Generate| SphereCreate
            SphereCreate -->|Place| NewPursuerPoint
            SphereCreate -->|Place| NewEvaderPoint

            CreateCrossEvaderEMA["Create Cross-Type Evader EMA Point"]
            SetCrossEvaderLocation["Set Location = Evader Point"]
            CreateCrossPursuerEMA["Create Cross-Type Pursuer EMA Point"]
            SetCrossPursuerLocation["Set Location = Pursuer Point"]
            CreateTypeEvaderEMA["Create Type-Specific Evader EMA Point"]
            SetTypeEvaderLocation["Set Location = Evader Point"]
            CreateTypePursuerEMA["Create Type-Specific Pursuer EMA Point"]
            SetTypePursuerLocation["Set Location = Pursuer Point"]

            NewEvaderPoint --> CreateCrossEvaderEMA
            CreateCrossEvaderEMA --> SetCrossEvaderLocation
            NewPursuerPoint --> CreateCrossPursuerEMA
            CreateCrossPursuerEMA --> SetCrossPursuerLocation
            NewEvaderPoint --> CreateTypeEvaderEMA
            CreateTypeEvaderEMA --> SetTypeEvaderLocation
            NewPursuerPoint --> CreateTypePursuerEMA
            CreateTypePursuerEMA --> SetTypePursuerLocation
        end

        CrossTypeDB[(Cross-Type Database)]
        TypeSpecificDB[(Type-Specific Database)]
        
        NewPursuerPoint -->|Store| TypeSpecificDB
        NewEvaderPoint -->|Store| TypeSpecificDB
        SetCrossEvaderLocation -->|Store| CrossTypeDB
        SetCrossPursuerLocation -->|Store| CrossTypeDB
        SetTypeEvaderLocation -->|Store| TypeSpecificDB
        SetTypePursuerLocation -->|Store| TypeSpecificDB
    end

    ExternalSources --> TokenCheck
```

</div>

# Cross-Type EMA Updates

<div align="center">

```mermaid
graph TD;
    ExternalSources["External Token Sources"]
    
    subgraph "Token Ingestion Point"
        TokenCheck{{"Token Type Exists?"}}
        UpdateProcess["Update Process"]
        
        CreateProcess["Create Process"]
        TokenCheck -->|Yes| UpdateProcess
        TokenCheck -->|No| CreateProcess

        subgraph "Cross-Type EMA Updates"
            UpdateCrossEvaderEMA["Update Cross-Type Evader EMA Point"]
            SetCrossEvaderLocation["Set Location = (α × Previous Location) + (1-α × Evader Point)"]
            UpdateCrossPursuerEMA["Update Cross-Type Pursuer EMA Point"]
            SetCrossPursuerLocation["Set Location = (α × Previous Location) + (1-α × Pursuer Point)"]

            UpdateProcess --> UpdateCrossEvaderEMA & UpdateCrossPursuerEMA
            
            UpdateCrossEvaderEMA --> SetCrossEvaderLocation
            UpdateCrossPursuerEMA --> SetCrossPursuerLocation
        end

        subgraph "Other Updates"
            TypeSpecificERA["Type-Specific EMA Updates"]
            PursuerEvader["Pursuer Evader Point Updates"]
            
            UpdateProcess --> TypeSpecificERA
            UpdateProcess --> PursuerEvader
        end

        CrossTypeDB[(Cross-Type Database)]
        TypeSpecificDB[(Type-Specific Database)]
        
        SetCrossEvaderLocation -->|Store| CrossTypeDB
        SetCrossPursuerLocation -->|Store| CrossTypeDB
        TypeSpecificERA -->|Store| TypeSpecificDB
        PursuerEvader -->|Store| TypeSpecificDB
    end

    ExternalSources --> TokenCheck
```

</div>

# Type-Specific EMA Updates

<div align="center">

```mermaid
graph TD;
    ExternalSources["External Token Sources"]
    
    subgraph "Token Ingestion Point"
        TokenCheck{{"Token Type Exists?"}}
        UpdateProcess["Update Process"]
        
        CreateProcess["Create Process"]
        TokenCheck -->|Yes| UpdateProcess
        TokenCheck -->|No| CreateProcess

        subgraph "Type-Specific EMA Updates"
            UpdateTypeEvaderEMA["Update Type-Specific Evader EMA Point"]
            SetTypeEvaderLocation["Set Location = (α × Previous Location) + (1-α × Evader Point)"]
            UpdateTypePursuerEMA["Update Type-Specific Pursuer EMA Point"]
            SetTypePursuerLocation["Set Location = (α × Previous Location) + (1-α × Pursuer Point)"]

            UpdateProcess --> UpdateTypeEvaderEMA & UpdateTypePursuerEMA
            
            UpdateTypeEvaderEMA --> SetTypeEvaderLocation
            UpdateTypePursuerEMA --> SetTypePursuerLocation
        end

        subgraph "Other Updates"
            CrossTypeERA["Cross-Type EMA Updates"]
            PursuerEvader["Pursuer Evader Point Updates"]
            UpdateProcess --> CrossTypeERA
            UpdateProcess --> PursuerEvader
        end

        CrossTypeDB[(Cross-Type Database)]
        TypeSpecificDB[(Type-Specific Database)]
        
        CrossTypeERA -->|Store| CrossTypeDB
        SetTypeEvaderLocation -->|Store| TypeSpecificDB
        SetTypePursuerLocation -->|Store| TypeSpecificDB
        PursuerEvader -->|Store| TypeSpecificDB
    end

    ExternalSources --> TokenCheck
```

</div>

# Pursuer and Evader Point Updates

<div align="center">

```mermaid
graph TD;
    ExternalSources["External Token Sources"]
    
    subgraph "Token Ingestion Point"
        TokenCheck{{"Token Type Exists?"}}
        UpdateProcess["Update Process"]
        CreateProcess["Create Process"]
        
        TokenCheck -->|Yes| UpdateProcess
        TokenCheck -->|No| CreateProcess

        subgraph "Point Updates"
            subgraph "Pursuer Point"
                UpdatePursuerPoint["Update Pursuer Point"]
                SetPursuerLocation["Set Location = Sum of:
                1. Move Towards Cross-Type Evader EMA Point by Ratio r
                2. Move Away from Type-Specific Evader EMA Point (inversely proportional to distance)"]
                UpdatePursuerPoint --> SetPursuerLocation
            end

            subgraph "Evader Point"
                UpdateEvaderPoint["Update Evader Point"]
                SetEvaderLocation["Set Location = Sum of:
                1. Move Away from Cross-Type Pursuer EMA Point by Ratio r
                2. Move Away from Type-Specific Pursuer EMA Point (inversely proportional to distance)"]
                UpdateEvaderPoint --> SetEvaderLocation
            end

            UpdateProcess --> UpdatePursuerPoint & UpdateEvaderPoint
        end

        subgraph "Other Updates"
            CrossTypeEMA["Cross-Type EMA Updates"]
            TypeSpecificEMA["Type-Specific EMA Updates"]
            
            UpdateProcess --> CrossTypeEMA & TypeSpecificEMA
        end

        TypeSpecificDB[(Type-Specific Database)]
        CrossTypeDB[(Cross-Type Database)]
        
        SetPursuerLocation -->|Store| TypeSpecificDB
        SetEvaderLocation -->|Store| TypeSpecificDB
        CrossTypeEMA -->|Store| CrossTypeDB
        TypeSpecificEMA -->|Store| TypeSpecificDB
    end

    ExternalSources --> TokenCheck
```

</div>

# Internal Token Formation Process

<div align="center">

```mermaid
graph TD;
    ExternalSources["External Token Sources"]
    InternalSources["Internal Token Sources"]
    
    subgraph "Token Ingestion Point"
        TokenCheck{{"Token Type Exists?"}}
        UpdateProcess["Update Process"]
        CreateProcess["Create Process"]
        
        TokenCheck -->|Yes| UpdateProcess
        TokenCheck -->|No| CreateProcess
    end

    subgraph "Token Formation Control"
        GetPursuerPoint["Get Pursuer Point"]
        FindNearestEvader["Find Nearest Different-Type Evader Point"]
        ComplexityCheck{"Adaptive Statistical Threshold Check (ASTC)
        Input: Complexity (Bits/TimeSpan)"}
        FormToken["Form Higher-Order Token from Evader and Pursuer Token Types"]
        
        UpdateProcess --> GetPursuerPoint
        GetPursuerPoint --> FindNearestEvader
        FindNearestEvader --> ComplexityCheck
        ComplexityCheck -->|Exceeds| FormToken
    end

    UpdateUtilityScores["Utility Score Management"]
    TokenInstanceControl --> UpdateUtilityScores
    TokenInstanceControl["Token Instance Control"]
    FormToken --> UpdateUtilityScores

    TypeSpecificDB[(Type-Specific Database)]
    
    ExternalSources --> TokenCheck
    InternalSources --> TokenCheck
    FindNearestEvader -->|Query| TypeSpecificDB
    GetPursuerPoint -->|Query| TypeSpecificDB
    FormToken -->|Feed Back| InternalSources
```

</div>

# Desirability and Undesirability Update Process

<div align="center">

```mermaid
graph TD;
    ExternalSources["External Token Sources"]
    
    subgraph "Token Ingestion Point"
        TokenCheck{{"Token Type Exists?"}}
        UpdateProcess["Update Process"]
        CreateProcess["Create Process"]
        
        TokenCheck -->|Yes| UpdateProcess
        TokenCheck -->|No| CreateProcess
    end

    subgraph "Desirability Score Updates"
        GetPursuerPoint["Get Pursuer Point"]
        FindNearestEvader["Find Nearest Different-Type Evader Point"]
        DesirabilityCheck{"Desirability?"}
        ControlState{"Control State?"}
        
        UpdateProcess --> GetPursuerPoint
        GetPursuerPoint --> FindNearestEvader
        FindNearestEvader --> DesirabilityCheck & ControlState
        
        SelectScores["Select Score Type to Update:
        • Suppressed Desirability
        • Uncontrolled Desirability
        • Triggered Desirability
        • Suppressed Undesirability
        • Uncontrolled Undesirability
        • Triggered Undesirability"]
        
        DesirabilityCheck -->|Desirable| SelectScores
        DesirabilityCheck -->|Undesirable| SelectScores
        ControlState -->|Suppressed| SelectScores
        ControlState -->|Uncontrolled| SelectScores  
        ControlState -->|Triggered| SelectScores
        
        UpdatePursuerTypeScore["Update Pursuer Type Score:
        1. Decay Current Score
        2. Add New Signal"]
        
        UpdateEvaderTypeScore["Update Evader Type Score:
        1. Decay Current Evader Score
        2. Decay Updated Pursuer Score
        3. Add Both Decayed Scores"]
        
        SelectScores --> UpdatePursuerTypeScore
        UpdatePursuerTypeScore --> UpdateEvaderTypeScore
    end

    TypeSpecificDB[(Type-Specific Database)]
    
    ExternalSources --> TokenCheck
    GetPursuerPoint -->|"Query Token Type<br/>(Point + Scores)"| TypeSpecificDB
    FindNearestEvader -->|"Query Token Type<br/>(Point + Scores)"| TypeSpecificDB
    UpdatePursuerTypeScore -->|Store Token Type| TypeSpecificDB
    UpdateEvaderTypeScore -->|Store Token Type| TypeSpecificDB
```

</div>

# External Token Triggering and Suppression Process

<div align="center">

```mermaid
graph TD;
    ExternalSources["External Token Sources"]
    
    subgraph "Token Ingestion Point"
        TokenCheck{{"Token Type Exists?"}}
        UpdateProcess["Update Process"]
        CreateProcess["Create Process"]
        
        TokenCheck -->|Yes| UpdateProcess
        TokenCheck -->|No| CreateProcess
    end

    subgraph "Token Instance Control"
        GetEvaderPoint["Get Evader Point"]
        FindNearestPursuer["Find Nearest Different-Type Pursuer Point"]
        UndesirabilityCheck{"ASTC on Uncontrolled Undesirability
        AND
        Suppressed Undesirability < Uncontrolled Undesirability"}
        
        DesirabilityCheck{"ASTC on Uncontrolled Desirability
        AND
        Triggered Desirability > Uncontrolled Desirability"}
        SuppressToken["Suppress Token Instance Type of Pursuer"]
        TriggerToken["Trigger Token Instance Type of Pursuer"]

        UpdateProcess --> GetEvaderPoint
        GetEvaderPoint --> FindNearestPursuer
        FindNearestPursuer --> UndesirabilityCheck
        UndesirabilityCheck -->|Yes| SuppressToken
        UndesirabilityCheck -->|No| DesirabilityCheck
        DesirabilityCheck -->|Yes| TriggerToken
    end

    UpdateUtilityScores["Utility Score Management"]
    InternalFormation["Internal Token Formation Process"] --> UpdateUtilityScores
    SuppressToken --> UpdateUtilityScores
    TriggerToken --> UpdateUtilityScores

    TypeSpecificDB[(Type-Specific Database)]
    
    ExternalSources --> TokenCheck
    GetEvaderPoint -->|Query| TypeSpecificDB
    FindNearestPursuer -->|Query| TypeSpecificDB
    SuppressToken -->|Feedback| ExternalSources
    TriggerToken -->|Feedback| ExternalSources
```

</div>

# Point Management Process

<div align="center">

```mermaid
graph TD;
    ExternalSources["External Token Sources"]
    
    subgraph "Token Ingestion Point"
        TokenCheck{{"Token Type Exists?"}}
        UpdateProcess["Update Process"]
        CreateProcess["Create Process"]
        
        TokenCheck -->|Yes| UpdateProcess
        TokenCheck -->|No| CreateProcess
    end

    subgraph "Point Management"
        FindLowestUtility["Find Point with Lowest Utility Score for Token Type"]
        
        CreateNewPoint["Create New Point:
        1. Get EMA Point Location for Same Token Type and Point Type
        2. Project Through Sphere Center to Opposite Side"]
        
        DeletePoint["Delete Single Point with Lowest Utility"]

        UtilityCheck{"High Adaptive Statistical Threshold Check (ASTC)
        Input: Utility
        k: k₁ ∈ (0,1) for creation"}
        
        LowUtilityCheck{"Low Adaptive Statistical Threshold Check (ASTC)
        Input: Utility
        k: k₂ ∈ (0,1) for deletion
        Note: k₂ < k₁"}

        UpdateProcess --> FindLowestUtility
        FindLowestUtility --> UtilityCheck
        UtilityCheck -->|Yes| CreateNewPoint
        UtilityCheck -->|No| LowUtilityCheck
        LowUtilityCheck -->|Yes| DeletePoint
    end

    TypeSpecificDB[(Type-Specific Database)]
    
    ExternalSources --> TokenCheck
    FindLowestUtility -->|Query| TypeSpecificDB
    CreateNewPoint -->|Store| TypeSpecificDB
    DeletePoint -->|Remove| TypeSpecificDB
```

</div>
