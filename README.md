# Claim Adjudicator

A notebook-based insurance claim review assistant built with LangChain, LangGraph, and Azure OpenAI. The app evaluates patient claims against policy coverage rules and produces an adjudication recommendation based on procedure coverage, diagnosis appropriateness, eligibility restrictions, and preauthorization requirements.

## Overview

This project demonstrates a ReAct-style agent that:

- retrieves patient information from a local dataset
- summarizes a patient's demographic and claim details
- looks up the associated insurance policy
- summarizes the policy's coverage rules and restrictions
- checks whether the procedure is covered, eligible, and compliant with policy requirements
- returns an approval or review recommendation

The workflow is implemented in `code.ipynb` and uses sample data stored under the `Data/` directory.

## Features

- Patient and policy lookup by ID
- LLM-powered patient record summaries
- LLM-powered insurance policy summaries
- Coverage validation for procedure/diagnosis matches
- Age, gender, and plan restriction checks
- Preauthorization validation
- Validation comparison against reference adjudication outputs
- Test-record processing for batch evaluation

## Repository layout

- `code.ipynb` — primary notebook implementing the workflow and agent logic
- `Data/` — sample records and validation data
  - `insurance_policies.json` — policy definitions and covered procedure rules
  - `reference_codes.json` — CPT and ICD-10 code dictionaries
  - `validation_records.json` — labeled validation patient records
  - `test_records.json` — additional records for evaluation
  - `validation_reference_results.csv` — expected adjudication results used for comparison
- `.env.example` — example environment settings
- `requirements.txt` — Python dependencies

## Tech stack

- Python
- LangChain
- LangGraph
- Azure OpenAI / Azure AD token auth
- Chroma vector store
- pandas
- httpx
- python-dotenv

## Setup

1. Create a virtual environment:

   ```bash
   python -m venv .venv
   source .venv/bin/activate
   ```

2. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. Configure environment variables:

   Copy `.env.example` to `.env` and add your Azure credentials:

   ```bash
   cp .env.example .env
   ```

   Then fill in:

   ```env
   AZURE_CLIENT_ID=your_client_id_here
   AZURE_CLIENT_SECRET=your_client_secret_here
   ```

   The notebook reads environment variables using `python-dotenv` and uses them to acquire an OAuth token for the configured Azure API gateway.

## Running the app

Open the notebook in VS Code or Jupyter and run the cells in order:

```bash
jupyter notebook code.ipynb
```

The notebook performs the following steps:

1. loads environment variables
2. configures Azure OpenAI clients
3. loads claim, policy, and reference datasets
4. defines tools for patient and policy summarization
5. builds a LangGraph ReAct agent
6. evaluates a claim by patient ID
7. compares results against validation reference data

## How the adjudication logic works

For each patient claim, the agent evaluates:

- whether the procedure code is covered by the policy
- whether the diagnosis code is accepted for that procedure
- whether the patient falls within the policy age range
- whether the patient meets any gender requirements
- whether the claim is subject to preauthorization and whether it was obtained
- whether other policy conditions or exclusions apply

The final decision is returned as either:

- `APPROVE`
- `ROUTE FOR REVIEW`

## Example workflow

A typical run requests the agent to evaluate a claim ID such as `P016` or a record ID from `test_records.json`. The agent then uses the configured tools to summarize the patient record and policy, compares the claim against policy restrictions, and returns a final adjudication outcome.

## Notes

- This repository is a prototype and notebook-first workflow rather than a production application.
- The project depends on external Azure credentials and service access.
- Sample policies and patient claims are intentionally synthetic and intended for evaluation and experimentation.

## License

This project does not currently include a license file. Add one if you intend to distribute or reuse the code publicly.
