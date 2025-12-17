# WS_PP
# Power Platform Hackathon – Vehicle Registration

The goal is **not** to build a production-ready system (in terms of stuff like SDLC, code architecture, ALM etc.), but to give pro developers a realistic, hands-on introduction to:

* Dataverse tables
* C# plug-ins
* (Optionally) form JavaScript
* A clean, repeatable developer workflow

---

## Repository Structure

```
/
├─ Plugins/
│  └─ (C# Dataverse plug-in project lives here)
│
├─ WebResources/
│  └─ (Optional JavaScript form logic – later in the workshop)
│
└─ index.md   ← you are here
```

For now, we will **only focus on the `Plugins` folder**.

---

## Prerequisites

Before starting, ensure the following are installed:

* **Power Platform CLI (pac)**
* Either:

  * Power Platform Tools extension for VS Code, **or**
  * PowerShell / terminal access

You will also need access to a **Dataverse environment** where you can:

* create tables
* register plug-ins

---

## Step 1 – Prepare the Tools

### Install Power Platform CLI

Verify installation:

```powershell
pac --version
```

---

### Launch Plugin Registration Tool (PRT)

We will need this tool later to register plug-ins.

Run:

```powershell
pac tool prt
```

This opens the **Plugin Registration Tool**.

> For now, you can minimize it — we will come back to it shortly.

---

## Step 2 – Create the Plug-in Project

Navigate into the `Plugins` folder:

```powershell
cd Plugins
```

Initialize a Dataverse plug-in project:

```powershell
pac plugin init
```

Choose:

* **Language**: C#
* **Type**: Dataverse plug-in

This scaffolds a **.NET Framework** plug-in project. This is expected and required for Dataverse plug-ins.

---

## Step 3 – Authenticate to Dataverse

Authenticate against your environment:

```powershell
pac auth create --url https://<your-org>.crmX.dynamics.com
pac auth select --url https://<your-org>.crmX.dynamics.com
```

Verify authentication:

```powershell
pac org who
```

---

## Step 4 – Generate Early-Bound Models (Filtered)

We want **strongly-typed entities**, but we do **not** want every table in the environment.

We therefore generate models **only for the tables used in this workshop**.

Example command:

```powershell
pac modelbuilder build \
  --outdirectory Models \
  --namespace Plugins.Models \
  --serviceContextName DataverseContext \
  --entitynamesfilter 'cr1d0_insurancecompany;cr1d0_leasingcompany;cr1d0_platesource;cr1d0_vehicleregistration;cr1d0_vehicletype'
```

> Note: the entity list **must be quoted** in PowerShell, otherwise only the first entity will be used.

This generates:

* strongly-typed entity classes
* a typed `DataverseContext`

These models will be used by the plug-in code.

---

## Step 5 – Implement the Plug-in

Inside the generated plug-in project, add business logic.

For this workshop, the plug-in will:

* validate input
* enforce business rules server-side

(Example snippets will be provided during the workshop.)

Key concepts to understand:

* Plug-ins run **server-side**
* They cannot be bypassed
* They complement client-side form logic

---

## Step 6 – Build the Plug-in

From the plug-in project folder:

```powershell
dotnet build -c Release
```

This produces a `.dll` file that will be deployed to Dataverse.

---

## Step 7 – Register the Plug-in (PRT)

Return to the **Plugin Registration Tool**:

1. Connect to your Dataverse environment
2. Register the compiled DLL as a new plug-in assembly
3. Register one or more steps (Create / Update)

This is the step where the plug-in is bound to Dataverse events.

---

## Step 8 – Test

Test behavior by:

* creating records
* updating records
* verifying that invalid data is rejected

At this point, the system should enforce rules regardless of UI.

---

## Optional – Fast Iteration with CLI

Once a plug-in is registered **once**, future updates can be deployed using:

```powershell
pac plugin push
```

This replaces the assembly without touching registered steps.

---

## Case Context – Vehicle Registration

During the hackathon, we work with a simplified version of the following case:

### Vehicle Registration Form

The form simulates a real-world registration process.

External systems (SQL, AI) are **not implemented**. Instead, test data is used to simulate pre-populated information.

### Core Responsibilities

* Capture vehicle registration details
* Apply validation rules
* Store data in Dataverse

### Example Business Rules

* Mandatory fields based on vehicle usage
* Dependencies between fields (e.g. leasing, business use)
* Validation of dates and identifiers

Server-side enforcement is handled by plug-ins.
Client-side guidance may be added later using form JavaScript.

---

## Next Steps

If time allows:

* Add simple JavaScript form logic under `WebResources/`
* Compare client-side vs server-side enforcement


Docs: 
*Plugins* [C# server side plugin docs](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/tutorial-write-plug-in)
*Webresources* (Client side scripting): [JS client side scripting](https://learn.microsoft.com/en-us/power-apps/developer/model-driven-apps/streamline-javascript-development-fiddler-autoresponder)

---

End of guide
