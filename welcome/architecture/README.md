---
description: 'The Web3 spending protocol explained.'
---

# Protocol Architecture

## Bando System Context 

```mermaid
    C4Context
      title System Context diagram for Bando Fulfillment Protocol
      System_Boundary(ecosystem, "EcosystemBoundary") {
        Person(spender, "Spender", "A crypto user who wants to spend its tokens in useful products & services")

        Enterprise_Boundary(b1, "Fulfuillment Providers") {
          System_Boundary(fs, "FupSystems") {
            System(FUPAPI, "REST API", "The exposed API to customers")
            System(WebHooksAPI, "WebHooks Notifications", "")
            SystemDb(SystemE, "FuP products database", "Stores all of the products information.")
          }
          System_Boundary(fts, "FupTechSupport") {
            System(FUPSupport, "", "")
            
          }
        }

        Enterprise_Boundary(bando, "Bando") {
          System_Boundary(bandosystems, "BFP") {
            System_Boundary(devtools, "Dev tools") {
              System(Widget, "Widget", "An embeddable widget")
              System(SDK, "SDK", "")
            }

            System_Boundary(sss, "Backend") {
                System(API, "API", "")
                System(backend, "Server side backend", "")
                
                SystemDb(db, "Products database", "Centralizes available products.")

                BiRel(API, backend, "uses")
                BiRel(backend, db, "uses")
            }

            System_Boundary(onchain, "On-chain") {
                System(smartcontracts, "EVM Smart contracts", "")
            }
          }
          System_Boundary(ftse, "Tech Support") {
            System(FUPSupporte, "", "") 
          }
        }

        Enterprise_Boundary(integrator, "Integrator") {
          Person(integrator_web_developer, "Web Developer", "JS, HTML skills needed")
          Person(integrator_backend_developer, "Backend Developer", "Any server side prog lang skill needed")
          
        }
      }

      BiRel(Widget, smartcontracts, "uses")
      BiRel(SDK, smartcontracts, "uses")
      BiRel(backend, smartcontracts, "uses")

      BiRel(spender, Widget, "Uses")
      BiRel(integrator_web_developer, Widget, "Integrates")
      BiRel(integrator_backend_developer, API, "uses")
      BiRel(integrator_backend_developer, SDK, "uses")

      BiRel(backend, FUPAPI, "uses")

```

## Bando Fulfillment Process

```mermaid
sequenceDiagram
    autonumber
    actor User
    participant Bando Widget
    participant Wallet
    box cyan On chain
      participant BandoRouter
      participant BandoRegistry
      participant BandoServiceEscrow
	    participant RefRegistry
	    participant BandoManagerContract
    end
    box pink Off chain
      participant Bando Fulfiller
      participant FuP
    end
    note over Bando Widget: Integrated on Wallet/Dapp
    note over BandoServiceEscrow: Minimal Proxies
    rect beige
    critical Fulfillment ref validation
    User->>Bando Widget: Select Service
    Bando Widget->>User: Ask for Reference
    User-->>Bando Widget: Inputs reference
    Bando Widget->>Bando Fulfiller: Requests Ref Validation
    Bando Fulfiller->>Bando Fulfiller: Generates Validation Ref
    Bando Fulfiller->>RefRegistry: Registers Validation ID
    Bando Fulfiller-->>Bando Widget: Approved Ref
    end
    end
    rect beige
    critical Request Fulfillment
    Bando Widget->>User: Prompt for payment details
    User-->>Bando Widget: Payment details
    Note over User, Bando Widget: User inputs token and amount
    Bando Widget ->>Wallet: Call BandoRouter
    Wallet->>User: Request Signature
    User->>Wallet: Sign Txn
    Wallet->>BandoRouter: Execute Txn
    BandoRouter->>RefRegistry: Validate Ref
    RefRegistry-->>BandoRouter: success
    BandoRouter->>BandoRegistry: Lookup Service
    BandoRegistry-->>BandoRouter: Service Details
    BandoRouter->>BandoServiceEscrow: Transfer Funds to escrow
    BandoServiceEscrow-->>BandoServiceEscrow: Emit ServiceRequested
    BandoServiceEscrow-->>Wallet: Txn hash
    Wallet-->>Bando Widget: Service Request Confirmed
    Bando Widget-->>User: Payment in progress
    end
    end
    rect beige
    critical Fulfill Request
    Bando Fulfiller->>BandoRouter: listen to ServiceRequested events
    Bando Fulfiller->>Bando Fulfiller: detect pending request event
    Bando Fulfiller-->Bando Fulfiller: pre-process fulfillment request
    Bando Fulfiller->>FuP: send fulfillment request to carrier
    FuP-->>Bando Fulfiller: carrier transaction response
    Bando Fulfiller->>BandoManagerContract: Register fulfillment result
    end
    end
```

<table data-view="cards">
  <thead>
    <tr>
      <th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden></th><th data-hidden data-card-target data-type="content-ref"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Payment Refs</strong></td><td>How the BFP validate payments</td><td></td><td></td><td><a href="payment-reference-validation.md">payment-reference-validation.md</a></td>
    </tr>
    <tr>
      <td><strong>Request a payment</strong></td><td>How the BFP routes payments</td><td></td><td></td><td><a href="order-request.md">order-request</a></td>
    </tr>
    <tr>
      <td><strong>Completing orders</strong></td><td>How the BFP make the requests whole</td><td></td><td></td><td><a href="order-fulfillment.md">order-fulfillment</a></td>
    </tr>
    <tr>
      <td><strong>Emitting refunds</strong></td><td>How the BFP authorizes and sends refunds</td><td></td><td></td><td><a href="refunds.md">refunds</a></td>
    </tr>
  </tbody>
</table>