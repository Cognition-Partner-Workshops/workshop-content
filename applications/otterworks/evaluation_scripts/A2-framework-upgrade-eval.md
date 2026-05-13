# Evaluation: A2 — Report Service Framework Upgrade

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. Evaluate whether the report-service framework upgrade has been completed by checking each criterion below.

Compare the branch against `main` to identify what the participant changed in `services/report-service/`.

## Criteria (all must pass)

1. **Java 17 target:** `services/report-service/pom.xml` sets `<java.version>17</java.version>` (or `<maven.compiler.source>17</maven.compiler.source>` and `<maven.compiler.target>17</maven.compiler.target>`).

2. **Spring Boot 3.2+:** The parent POM references Spring Boot 3.2.x or later (check `<parent>` section for `spring-boot-starter-parent` version).

3. **javax → jakarta migration:** No `javax.*` imports remain in any Java source file under `services/report-service/src/`. All should be replaced with `jakarta.*` equivalents. Run: `grep -r "import javax\." services/report-service/src/` — this should return zero results.

4. **JUnit 5 migration:** Test files under `services/report-service/src/test/` use JUnit 5 annotations (`org.junit.jupiter.api.Test`, `@BeforeEach`, `@ExtendWith`) instead of JUnit 4 (`org.junit.Test`, `@Before`, `@RunWith`).

5. **SpringFox → springdoc-openapi:** `pom.xml` no longer references `springfox`. Instead, `springdoc-openapi-starter-webmvc-ui` (or equivalent springdoc 2.x artifact) is present.

6. **PDF library updated:** `PdfReportGenerator.java` (or equivalent) no longer uses iText 5 (`com.itextpdf.text`). Uses either OpenPDF (`com.lowagie.text`) or iText 7+ (`com.itextpdf.kernel`).

7. **Commons Lang 3:** No references to `org.apache.commons.lang.` (lang2). All replaced with `org.apache.commons.lang3.`.

8. **Tests pass:** Run `cd services/report-service && mvn test`. All tests must pass. Report the test count and any failures.

9. **Build succeeds:** Run `cd services/report-service && mvn package -DskipTests`. Build must succeed without errors.

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Java 17 target: PASS/FAIL — [details]
2. Spring Boot 3.2+: PASS/FAIL — [details]
3. javax → jakarta: PASS/FAIL — [details]
4. JUnit 5: PASS/FAIL — [details]
5. SpringFox → springdoc: PASS/FAIL — [details]
6. PDF library updated: PASS/FAIL — [details]
7. Commons Lang 3: PASS/FAIL — [details]
8. Tests pass: PASS/FAIL — [test count, failures]
9. Build succeeds: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS only if ALL 9 criteria pass.
