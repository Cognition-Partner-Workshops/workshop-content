# Lab: Report Service Framework Upgrade

## Objective

Upgrade the report-service from Java 8 / Spring Boot 2.5 to Java 17 / Spring Boot 3.2+ while keeping all tests green.

## What's Wrong

The report-service (`services/report-service/`) is frozen on Java 8 and Spring Boot 2.5.14 with a stack of outdated dependencies. Every dependency has a known upgrade path, but the upgrades are interconnected: changing Spring Boot requires changing the `javax` to `jakarta` namespace, which affects every import in the codebase. The service has multiple CVEs from Guava 28, and iText 5.5 has license concerns (AGPL).

The full list of upgrade axes:
1. **Java 8 to Java 17** — source/target in `pom.xml`
2. **Spring Boot 2.5.14 to 3.2+** — parent POM version
3. **`javax.*` to `jakarta.*`** — all imports throughout the codebase
4. **JUnit 4 to JUnit 5/Jupiter** — test framework and annotations
5. **SpringFox 3.0 to springdoc-openapi 2.x** — API documentation (SpringFox is abandoned)
6. **iText 5.5.13 to OpenPDF or iText 7+** — PDF generation library (license change)
7. **Commons Lang 2.6 to commons-lang3** — package rename (`org.apache.commons.lang` to `org.apache.commons.lang3`)
8. **Commons IO 2.6 to 2.15+** — version bump
9. **Guava 28 to 33+** — CVE remediation
10. **Apache POI 4.1.2 to 5.2+** — version bump
11. **Mockito 3.12 to 5.x** — test mocking framework

## Where to Look

- `services/report-service/pom.xml` — All dependency versions are declared here. Comments mark each legacy dependency.
- `services/report-service/src/main/java/com/otterworks/report/` — All source files use `javax.*` imports
- `services/report-service/src/main/java/com/otterworks/report/config/SwaggerConfig.java` — SpringFox configuration
- `services/report-service/src/main/java/com/otterworks/report/service/PdfReportGenerator.java` — iText 5 usage
- `services/report-service/src/main/java/com/otterworks/report/service/ExcelReportGenerator.java` — Apache POI usage
- `services/report-service/src/test/` — Test files using JUnit 4 and Mockito 3
- `services/report-service/UPGRADE_GUIDE.md` — **Read this first.** It documents every upgrade axis with specific files, imports, and verification steps.

## What Done Looks Like

- [ ] `pom.xml` updated: Java 17 source/target, Spring Boot 3.2+ parent, all dependencies at target versions
- [ ] All `javax.*` imports replaced with `jakarta.*`
- [ ] SpringFox replaced with springdoc-openapi 2.x (annotations and config)
- [ ] iText 5 replaced with OpenPDF or iText 7+ in `PdfReportGenerator.java`
- [ ] Commons Lang 2.6 replaced with commons-lang3 (package rename in all affected files)
- [ ] JUnit 4 annotations (`@Test`, `@Before`, `@RunWith`) replaced with JUnit 5 equivalents (`@Test` from Jupiter, `@BeforeEach`, `@ExtendWith`)
- [ ] All existing tests pass: `cd services/report-service && mvn test`
- [ ] Application builds successfully: `cd services/report-service && mvn package -DskipTests`
- [ ] No CRITICAL or HIGH CVEs from the upgraded dependencies

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `services/report-service/UPGRADE_GUIDE.md` for the complete axis-by-axis plan. The guide tells you exactly which files to change and what the before/after looks like for each axis.

### Hint 2 — Specific Direction

Start with the `pom.xml` changes (Java version, Spring Boot parent, dependency versions). Then do the `javax` to `jakarta` namespace migration across all source files. Run `mvn test` after each axis to catch regressions early.

### Hint 3 — Approach

Ask Devin to read `services/report-service/UPGRADE_GUIDE.md` and then execute the upgrade axes one at a time, running `mvn test` after each to verify. Start with Java 17 + Spring Boot 3.2, then `javax` to `jakarta`, then the remaining library upgrades.

## Time Budget

- ~60-90 minutes for the full 11-axis upgrade
- The `javax` to `jakarta` migration is the most impactful (touches every file) but mechanical
- The iText and SpringFox replacements require understanding API differences
