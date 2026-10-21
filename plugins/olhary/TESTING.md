# Testing the Olhary plugin

How to try every part of this plugin, written for store reviewers and anyone else.

## The test account

Store reviewers get an email and password in the submission form. It signs in with no second step
and no code sent by email.

Olhary's tools read public data (PNCP, the sanctions registers, the catalogs, legislation and IBGE),
so this account sees the same answers as any other. The account holds no private data of its own,
and the tools cannot read any.

## Sign in

1. Install the plugin (see [README.md](README.md)).
2. Claude Code: type `/mcp`, pick olhary, choose Authenticate. Codex: `codex mcp login olhary`
   (Codex 0.147 or later). ChatGPT and claude.ai: add the connector, then press Connect.
3. olhary.ai opens in your browser. Sign in with the test account's email and password.
4. The next page says "Connect" (Conectar in Portuguese) and the app's name. Check the address
   under "Returns to" (Volta para), where the app gets its answer, then press Connect.

The app gets its own key, named after the app. To disconnect, revoke it in the account's
Settings > API keys (Configurações > Chaves de API in Portuguese). The account holds up to 10
keys, so revoke old ones if a review runs into that limit.

## Should work

The data changes every day, so the lists below say what each answer holds, not exact numbers. The
municipality and CNAE codes never change.

| Ask                                                                                              | What happens                                                                                                                                                                      |
| ------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "What is the IBGE code of Campinas, SP?"                                                         | Calls `search_municipalities` with `query` "Campinas" and `uf` "SP". Shows Campinas, São Paulo, with code 3509502.                                                                |
| "How many people live in the municipality with IBGE code 3509502, and what is its GDP?"          | Calls `get_municipality`. Shows the population, the GDP in thousands of reais and the reference year.                                                                             |
| "What does CNAE 6201-5/01 cover?"                                                                | Calls `get_cnae` with `code` "6201-5/01". Shows the code's description, its activities, and its class, group, division and section.                                               |
| "Find the CATMAT code for A4 copy paper."                                                        | Calls `search_catalog_items` with a query and `type` "material". Shows items with their code, description and class.                                                              |
| "Find tenders about office chairs that are still open for proposals in São Paulo state. Show 5." | Calls `search_procurements` with a query, `uf` "SP", `lifecycleStatus` "recebendo_proposta" and `limit` 5. Shows up to 5 tenders with the agency, place, amount and closing date. |
| "Is the company with CNPJ 00.000.000/0001-91 on a public sanctions list?"                        | Calls `get_company_sanctions`. Shows whether the registers were verified (`verificado`), the date of each, and the records found, if any.                                         |
| "What has the company with CNPJ 00.000.000/0001-91 won in public tenders?"                       | Calls `get_company_procurement_performance`. Shows its recorded wins, awarded values and totals by period, and says when the evidence is thin (`lowData`).                        |
| "What does the federal legislation say about dispensa de licitação?"                             | Calls `search_legislation`. Shows passages with their law and label, and reminds you to check the official text.                                                                  |

To send no text to Google, ask for a tender search with filters only (no words to search for). The
`search_procurements` tool says in its description that a text query goes to Google's Gemini API.

## Should not work

| Ask                                                              | What happens                                                                                                              |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| "Look up the person with CPF 123.456.789-09."                    | No tool takes a CPF or a person's name. The agent says Olhary's connector reads only companies, tenders and public codes. |
| "Show my Olhary chat history and my saved documents."            | No tool reads the account. The agent says it cannot.                                                                      |
| "Revoke my other Olhary keys and change my account email."       | No tool creates or revokes keys or changes the account. Only you can, in olhary.ai.                                       |
| "Save this tender and email me when a similar one is published." | No tool saves, watches or sends anything. The agent says the connector only reads.                                        |

## What each tool can change

Every tool says what it does through `readOnlyHint`, `destructiveHint` and `openWorldHint`.

- All eight tools read, so `readOnlyHint` is true and `destructiveHint` is false for each. None
  writes to a government system or to your account.
- `search_procurements` has `openWorldHint` true, because a text query goes to Google's Gemini API
  (a service outside Olhary) to find similar results.
- The other seven read Olhary's own copy of the public data, so `openWorldHint` is false.
- Olhary logs each lookup, as its privacy policy says in section 5.1, and the two company tools
  keep a cached copy of the public answer. Neither changes anything you can see or own, and the
  tools cannot read or change them.
