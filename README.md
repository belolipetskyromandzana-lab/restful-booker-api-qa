# Restful-Booker API tests

Postman collection and test-case matrix for the Restful-Booker demo API.

API documentation: https://restful-booker.herokuapp.com/apidoc/index.html

## Files

- `test-cases.xlsx` — 36 positive and negative test cases, with request IDs matching the Postman collection.
- `postman/Restful-Booker.postman_collection.json` — executable requests and assertions.
- `postman/Restful-Booker.postman_environment.json` — base URL and values shared by the requests.
- `bug-reports.xlsx` — discrepancies found in the supplied API documentation.
- `reports/validation.json` — local structural checks and the status of the live connectivity check.

## Run in Postman

1. Import both JSON files from `postman/`.
2. Select the `Restful-Booker` environment.
3. Open **Restful-Booker API Tests** and run the whole collection from top to bottom.
4. Review each assertion in the Collection Runner. The committed workbook records the results of the 2026-10-21 run; update statuses after each new run.

The collection is ordered deliberately. `BOOK-01` creates the fixture booking, stores its ID and test data in the environment, and later search/read/update/delete requests use those values. Running a single later request without first running `BOOK-01` will leave its variables empty.

`DELETE-04` removes the fixture booking. The suite writes test data to the demo API; do not point it at a production service.

## Run with Newman

With Node.js available, the collection can also be run from the repository root:

```bash
npx newman run postman/Restful-Booker.postman_collection.json \
  -e postman/Restful-Booker.postman_environment.json
```

## Test scope

The suite covers:

- token creation and invalid/malformed authentication input;
- booking creation and payload-validation cases;
- listing and filtering booking IDs by name and dates;
- JSON and XML representations of a booking;
- full update (`PUT`) and partial update (`PATCH`);
- authorization checks for write operations;
- deletion and verification that the fixture is no longer available;
- the `/ping` health endpoint.

The test-case workbook distinguishes documented behaviour from expectations used to probe validation. The API document does not define an exact error status for every invalid payload, so a failed negative assertion is a reason to inspect the response, not by itself proof of a product defect.

## Notes on the supplied specification

The supplied documentation has a few internal inconsistencies. The PATCH example uses `curl -X PUT`; the DELETE section says `200` in one place and shows `201 Created` in another; the health-check section has the same status-code conflict. The PATCH header notes also mention PUT. These are recorded in `bug-reports.xlsx` as documentation findings. They have not been reported as runtime API defects.

For DELETE and `/ping`, the collection accepts either `200` or `201` until the expected contract is clarified. This avoids treating an inconsistency in the supplied specification as a confirmed API failure.

## Execution status

The collection and its JavaScript were checked locally for structure and syntax. A Newman run against the demo API was performed on 2026-10-21. All 36 requests completed without request execution errors. The test-case workbook records 29 passed cases and 7 failed cases; 45 of 53 assertions passed and 8 failed. These failures require investigation and are not, by themselves, proof of confirmed API defects. Results may also be affected by the behavior of the public demo service and its shared test data.

The detailed Newman report (`reports/newman-latest.json`) is kept locally and excluded from Git because it contains environment values and response bodies. `reports/validation.json` retains the result of an earlier connectivity attempt on 2026-10-16, when DNS resolution failed; it does not represent the latest live test run.

## Repository

This project is published at https://github.com/belolipetskyromandzana-lab/restful-booker-api-qa.

Clone the repository with:

```bash
git clone https://github.com/belolipetskyromandzana-lab/restful-booker-api-qa.git
cd restful-booker-api-qa
```
