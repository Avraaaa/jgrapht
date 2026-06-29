# Prompt Log — Repository Selection and Refactoring PR Planning

This file records the important prompts used during repository selection and project planning. It includes only the most relevant prompts and the key response outcomes.

---

## Prompt 0 — Initial Deep Research Prompt

Exact prompt used:

```text
You are my Deep Research assistant. I need you to find appropriate GitHub repositories for a university software engineering project called “Code Smells in the Wild.”

The assignment requires each student to choose one unique real-world open-source Java repository, analyze its code quality, identify maintainability/design issues, refactor some of those issues, validate that functionality is unchanged, document the process, and submit a Pull Request.

My deadline is in about 2–3 weeks, so repository choice matters a lot. Do not just list popular Java repositories. I need repositories where a realistic, small-to-medium refactoring PR can be prepared quickly and has some chance of being welcomed.

Repository hard requirements:

* Public GitHub repository.
* Written mainly in Java.
* Prefer pure Java projects.
* Avoid Spring Boot or heavy enterprise frameworks unless the repo is unusually manageable.
* At least 500 GitHub stars.
* At least around 100 commits.
* Actively maintained.
* Prefer repositories with recent commits, recent releases, recently merged PRs, or recent maintainer responses.
* Recommended size: around 2,000–50,000 lines of code.
* Must have build instructions and should be reasonably possible to clone, build, and run locally.
* Must contain enough understandable code for a student to analyze and refactor.

Very important selection nuance:
A lot of repositories with obvious code smells do not care about code-quality PRs. On the other hand, repositories whose maintainers care a lot about code quality often have fewer obvious smells. I need repos in the middle: active enough, maintainers responsive enough, but still with realistic maintainability/design/code-smell issues that can be improved safely.

Find at least 50 candidate repositories.

Prioritize interesting, understandable topics such as:

* CLI tools
* developer tools
* file parsers
* PDF/image/audio/text processing libraries
* algorithms/data structure libraries
* small databases/search/indexing tools
* games or educational tools
* testing utilities
* JSON/XML/CSV tools
* compression/archive tools
* networking utilities
* security/crypto utilities, only if safe and understandable
* Java libraries with clear modules and tests

Avoid or strongly penalize:

* Huge enterprise frameworks
* Spring Boot applications
* Massive monorepos
* Android-heavy repositories unless they are easy to build and mostly Java
* Repositories with no recent activity
* Repositories that only accept feature PRs and reject refactoring/style cleanup
* Repositories with no tests or impossible local setup
* Repositories that are too polished and have almost no code smells
* Repositories where the only possible refactoring would require a huge architecture rewrite
* Repositories with generated code as the main source of smells

Research method:

1. Use GitHub search and GitHub repository metadata.
2. Verify stars, commits, recent activity, language breakdown, build system, tests, and approximate LOC.
3. Inspect source files manually through GitHub.
4. Check recent Issues and Pull Requests for maintainer responsiveness.
5. Search closed PRs for terms like:

   * refactor
   * cleanup
   * maintainability
   * readability
   * code quality
   * simplify
   * remove duplication
   * extract method
   * reduce complexity
6. Use static-analysis clues when possible:

   * PMD
   * Checkstyle
   * SpotBugs
   * SonarCloud/SonarQube if available
   * GitHub CodeQL warnings if visible
   * IntelliJ-style inspection clues from code structure
7. Do not invent issues. For every repo, provide concrete likely smell evidence based on visible code, file names, class names, method names, or issue/PR discussions.
8. If you cannot verify something, mark it as “Needs verification,” not as fact.

For each of the 50+ repositories, provide:

1. Rank
2. Repository name
3. GitHub URL
4. Topic/category
5. Short description of what the project does
6. Stars
7. Approximate commits
8. Approximate Java percentage
9. Approximate source LOC, or “Needs verification”
10. Build system: Maven, Gradle, Ant, etc.
11. Last commit date
12. Recent activity evidence:

    * recent commits
    * recent releases
    * merged PRs
    * maintainer responses
13. Why the repo is interesting
14. Why it is manageable for a 2–3 week student project
15. Code smell / maintainability issues found:

    * Give at least 3 likely issues per repo if possible.
    * Examples: large classes, long methods, duplicated logic, complex conditionals, poor naming, dead code, tight coupling, utility-class bloat, God classes, feature envy, primitive obsession, inconsistent exception handling, repeated validation logic, unclear module boundaries, test duplication, magic numbers, poor separation of concerns.
16. Evidence for each smell:

    * Link to file/class/method/issue/PR where possible.
    * Mention exact file paths when possible.
17. Suggested small-to-medium refactoring PR:

    * What change I could realistically make.
    * Why it improves readability/maintainability.
    * Why it is low-risk.
18. Test/validation plan:

    * What tests to run.
    * How to show behavior unchanged.
19. PR acceptance likelihood:

    * High / Medium / Low
    * Explain using evidence from recent PR activity and maintainer behavior.
20. Risk level:

    * Low / Medium / High
    * Explain risks: difficult build, too large, inactive maintainers, too complex, no tests, strict contribution rules, etc.
21. Overall suitability score out of 100.

After listing at least 50 repositories, give:

* Top 10 final shortlist.
* Top 3 safest choices.
* Top 3 most interesting choices.
* Top 3 best chances for accepted PR.
* Top 3 easiest to understand quickly.
* Repositories to avoid and why.
* Recommended GitHub search queries used.
* A final recommendation for which repo I should pick first, second, and third.

Scoring formula:

* Assignment fit: 20 points
* Active maintenance: 20 points
* Manageable size/build: 15 points
* Clear code smells: 20 points
* Refactoring feasibility in 2–3 weeks: 15 points
* PR acceptance likelihood: 10 points

Output format:
Use a large markdown table for the 50+ repo list. Then provide detailed notes for the top each. Be strict. I prefer fewer hallucinations and more “Needs verification” labels over confident but fake claims.
Also rank them in a tierlist based on the scoring criteria my teacher provided.

actually not just github, search for repos in articles or other places too. but properly verify that it meets the requiremetns and currently actaully has the issues

also check how long the issue has been unsolved and how difficult it is
```

Assistant response outcome:
A large repository search and ranking was requested, with strict verification requirements and an emphasis on avoiding hallucination.

---

## Prompt 1 — Checking First Five Repositories

Exact prompt used:

```text
explore the first 5 repos here.  then find open issues on these repos regarding code smell https://refactoring.guru/refactoring/smells
i have also uploaded my teacher's requirements
```

Assistant response outcome:
The first five repositories from the uploaded list were checked: OpenFeign, Java-WebSocket, java-jwt, Dropwizard Metrics, and Kryo. The response mapped visible issues to possible Refactoring.Guru smells, but many were later rejected because they were feature requests or behavior-changing bug fixes rather than pure refactoring issues.

---

## Prompt 2 — Narrowing to Refactoring-Only Issues

Exact prompt used:

```text
it has to be a specifically refactoring kind of issues not something that adds new features or does bug fixes that fundamentally change the behavior
```

Assistant response outcome:
The filtering rule was tightened. Issues were rejected unless they were explicitly cleanup/refactoring/internal-structure related and behavior-preserving. Most earlier candidates were rejected under this stricter rule.

---

## Prompt 3 — Checking More Repository Batches

Exact prompt used:

```text
can you try teh next 10 repos then. same requirements
```

Assistant response outcome:
The next group of repositories was checked under the same refactoring-only rule. Most were rejected because their open issues were bugs, feature requests, behavior changes, dependency work, or public API changes rather than pure refactoring.

---

## Prompt 4 — Checking JTS Issues and More Repositories

Exact prompt used:

```text
https://github.com/locationtech/jts/issues/1149
https://github.com/locationtech/jts/issues/1113

check these 2 issues, and also try teh next 15 repos
```

Assistant response outcome:
JTS issue #1113 was identified as a possible maintainability/refactoring-style issue about raw collection return types and generics, but it had a warning because it may involve public API signatures. More repositories were checked, but most did not provide clean refactoring-only targets.

---

## Prompt 5 — Switching From Open Issues to Repositories That Accept Code-Smell PRs

Exact prompt used:

```text
so i am assuming i wont get any issue that are open, and rather have to find a repo that are welcome to me finding code smell related issues and submitting myself. these are the other two repo lists. so among all these which repos fit hte criteria, pls properly check everything and then return the results. i would prefer no hallucination, take longer if u need
```

Assistant response outcome:
The strategy shifted from finding existing open refactoring issues to finding repositories that might accept a small self-found behavior-preserving code-smell/refactoring PR. Several candidate repositories were evaluated by activity, size, contribution friendliness, and code-smell potential.

---

## Prompt 6 — Checking Direct PR Possibility

Exact prompt used:

```text
is there any repo where its acceptable to submit pull request directly without having to open issue and they are open to refactoring prs pls check in detail from teh above repo lists taking as much time as u need
```

Assistant response outcome:
Repositories were checked based on whether their contribution policy allowed direct pull requests without first opening an issue. JSON-java, commonmark-java, JLine3, OpenFeign, cron-utils, and others were discussed, but later several were rejected because contribution policies or project size made them unsafe.

---

## Prompt 7 — Rejecting JLine3 Because It Is Too Large

Exact prompt used:

```text
jline3 codebase is too big, my teacher wants me to understand the core modules search the repolist from the pdfs i uploaded
```

Assistant response outcome:
JLine3 was dropped as a primary option because its codebase was too large for the assignment’s “understand the core modules” expectation. Smaller repositories from the uploaded lists were considered instead.

---

## Prompt 8 — Rechecking Contribution Policies

Exact prompt used:

```text
bro check how does these repos handle contributions the one u just gave, also cron utils is dead properly check man i dont care if itakes u 5 years
```

Assistant response outcome:
The earlier suggestions were rechecked more strictly using contribution policies, release activity, PR backlog, and maintainer responsiveness. cron-utils was dropped due to weak maintenance/review signals.

---

## Prompt 9 — Rejecting Repositories Whose CONTRIBUTING.md Blocks Refactoring

Exact prompt used:

```text
bro greenmail legit said it doesnt support refactoring things in contributing.md pls properly check contributing.md first if the repo has it. and i manually checked the repo u suggestd so far, doesnt feel my criteria find more from teh 3 pdfs
```

Assistant response outcome:
The repository filter was tightened again: if a repository had a CONTRIBUTING.md, that file had to be checked first. GreenMail was dropped. More candidates were searched from the uploaded PDF lists.

---

## Prompt 10 — Searching More Repositories

Exact prompt used:

```text
find more
```

Assistant response outcome:
More repositories were checked with contribution policy as the first gate. EqualsVerifier, NullAway, JUnit4, AssertJ, and cache2k were discussed. Some were considered possible, but concerns remained about code-smell density, maintenance mode, or strict contribution rules.

---

## Prompt 11 — JGraphT Contribution Page Review

Exact prompt used:

```text
Skip to content
jgrapht
jgrapht
Repository navigation
Code
Issues
101
 (101)
Pull requests
19
 (19)
Agents
Actions
Projects
Wiki
Security and quality
Insights
Dev guide: How to make your first (code) contribution

Sung Ho Yoon edited this page on Mar 22, 2024 · 3 revisions
Contributions to JGraphT can be made in many forms: through code, by answering questions on the mailing list, by updating our website, or simply by fixing javadoc typos. Any form of contribution is welcome! This guide is intended as a roadmap to a successful code contribution.

Setup your development environment
Before you start implementing, please discuss your idea with us first. Sometimes, functionality already exists, is already being implemented by someone else, or is simply not a good fit for jgrapht. For ideas on what to implement, see the "Open tasks, projects and collaboration ideas" page (we try to keep this page up-to-date, but it is better to check whether some task has already been performed). Notice that algorithmic implementations must be based on academic publications (books, papers, phd dissertations).
Take note of the contributor guidelines
a. Writing a new class or package.
In order to write a new class or package, follow these steps:

Each new class you add needs a copyright header. This header can be found in any of the existing classes. Simply copy and paste the header. The only modifications you are allowed to make to the header are the year, and your name on the first line. The first line of the header should read:
  * (C) Copyright <current year>-<current year>, by <your name> and Contributors.
Here's an example:
 * (C) Copyright 2017-2017, by Barak Naveh and Contributors.
It is very important that you strictly adhere to this format. Copyright headers are updated by automated scripts; deviation from the default format interferes with the automated update process.
Implement your classes. Have a look at the structure and style of existing classes with similar functionality. Do not modify HISTORY.md or CONTRIBUTORS.md yourself; we do that for you. This prevents a lot of merge conflicts.
Every new class you write needs to be accompanied by tests
Write proper documentation.
OPTIONAL: Whenever you implement a new algorithm, make sure to place it in the right package. Moreover, your algorithm should implement the correct interface from org.jgrapht.alg.interfaces. If no suitable package exists, create a new one, and add a new package-info.java file containing the package description. If applicable, consider adding a new interface to org.jgrapht.alg.interfaces.
b. Augmenting existing code.
Whenever you are making modifications to existing code, e.g. fixing a bug, or adding additional functionality, follow these steps:

Make your modifications to the existing code. Keep style and structure consistent. Do not modify HISTORY.md or CONTRIBUTORS.md yourself; we do that for you. This prevents a lot of merge conflicts.
In case you added new functionality, write proper documentation and corresponding unit tests.
Run the existing unit tests to ensure that you didn't break anything.
In case you made considerable modifications to a file (not just a typo), consider adding yourself to the @author list. Do not modify the Copyright header by adding your name to it! You are covered by the "and Contributors" clause in the copyright header.
If your change required edits in any example code, please make sure that the corresponding tutorial pages remain consistent.
Finishing your contribution
Add, commit and push your changes to your repository.
Verify whether your code passes all our tests, and meets our style and coding conventions. To do this, run:
mvn clean
mvn verify
mvn checkstyle:check -P checkstyle
mvn javadoc:aggregate
In case of errors/warnings, go back and fix them.
Once you are satisfied (i.e completed your code, documentation, and tests following the instructions above), create a Pull Request. Provide a proper description for the reviewers: what is it that you are submitting? Are you fixing a bug? If so, describe the bug, and how you fixed it.
Wait for a code review. This may take some time. In case changes are required, add, commit and push your changes to your branch. The Pull Request gets automatically updated.
..
https://github.com/jgrapht/jgrapht/issues/1056


Check these issue, and check how contribution friendly are they based on their contribution.md

```

Assistant response outcome:
Verdict: inconclusive
I cannot honestly mark #1056 fixed or persisting because the primary signal is Eclipse Problems View warnings, and this machine has no local eclipse.exe or reusable JDT/ECJ runtime available. I did verify current upstream: local master and origin/master both point to e3adbc2f409a06a23d8c6b4ecef9420d2bdc893a (Bump com.puppycrawl.tools:checkstyle from 13.5.0 to 13.6.0 (#1354), 2026-06-15).
Evidence Summary
Area	Result
git status --short	only untracked AGENTS.md, context/PMD files; git ignore warning from user config permission
Java	Oracle JDK 25.0.2; project compiles with release 21
Maven	Apache Maven 3.9.16
mvn test	passed after sandbox network approval
mvn verify	passed
mvn -P checkstyle validate	passed, 0 Checkstyle violations
requested mvn -DskipTests -Dmaven.compiler.showWarnings=true -Dmaven.compiler.showDeprecation=true testCompile	failed literally: PowerShell split dotted -D, then Maven rejected testCompile because valid phase is test-compile
corrected mvn -DskipTests "-Dmaven.compiler.showWarnings=true" "-Dmaven.compiler.showDeprecation=true" test-compile	passed; warnings were production/module/assembly, not #1056 test deprecated/unused categories

Eclipse/JDT
Category	Count
deprecated API/JUnit	not measured
unused local/member/method/assignment/import	not measured
unchecked/raw	not measured
other	not measured

Reason: no Eclipse installation found in PATH, user profile, or Program Files; no JDT/ECJ runtime found. Repo only has etc/org.eclipse.jdt.core.prefs, which is formatter config, not enough to reproduce Problems View.
A. Eclipse test warnings matching #1056
Tool	File	Line	Rule/message
Eclipse/JDT	N/A	N/A	Not measured; tool unavailable

PMD Analysis
pmd-smells.txt: 342 raw PMD records, 171 unique after dedupe by class/file+line+rule+message.
Scope	Unique PMD findings
src/test/java	0
src/main/java	171
unresolved initially	1, mapped manually to jgrapht-core/src/main/java/org/jgrapht/alg/scoring/EdgeBetweennessCentrality.java:247

B. PMD test findings matching #1056
Tool	File	Line	Rule/message
PMD	N/A	N/A	0 unique PMD findings in src/test/java

C. Useful PMD findings outside #1056
Tool	File	Line	Rule/message
PMD	jgrapht-core/src/main/java/org/jgrapht/alg/flow/GusfieldEquivalentFlowTree.java	83	UnusedPrivateField: lastInvokedSource
PMD	jgrapht-core/src/main/java/org/jgrapht/alg/flow/GusfieldEquivalentFlowTree.java	84	UnusedPrivateField: lastInvokedTarget
PMD	jgrapht-core/src/main/java/org/jgrapht/alg/shortestpath/BoundedPrunedYenKShortestPath.java	194	UnusedFormalParameter: sink
PMD	jgrapht-core/src/main/java/org/jgrapht/alg/shortestpath/DeltaSteppingShortestPath.java	89	UnusedPrivateField: DEFAULT_PARALLELISM
PMD	jgrapht-core/src/main/java/org/jgrapht/alg/cycle/HawickJamesSimpleCycles.java	97	ClassCastExceptionWithToArray
PMD	jgrapht-core/src/main/java/org/jgrapht/alg/cycle/JohnsonSimpleCycles.java	279	ClassCastExceptionWithToArray
PMD	jgrapht-core/src/main/java/org/jgrapht/alg/cycle/SzwarcfiterLauerSimpleCycles.java	221	ClassCastExceptionWithToArray
PMD	jgrapht-core/src/main/java/org/jgrapht/generate/netgen/NetworkGenerator.java	540	JumbledIncrementer

Other PMD rule counts outside #1056: UselessParentheses 62, CollapsibleIfStatements 20, UnnecessaryFullyQualifiedName 16, AvoidBranchingStatementAsLastInLoop 8, UnnecessaryModifier 7, EmptyCatchBlock 4, EmptyControlStatement 2, TooManyStaticImports 2, UselessOverridingMethod 2.
PR Recommendation
Do not open a narrow #1056 PR from this evidence. Best action: leave a stale issue comment saying current upstream has 0 PMD test findings matching #1056, 0 legacy JUnit imports in tests, Maven/checkstyle pass, but Eclipse Problems View could not be reproduced here. Open a separate PMD cleanup PR only for production-code findings if maintainers want that cleanup.


JGraphT was classified as contribution-friendly but discussion-first. The guide asks contributors to discuss ideas before implementation, so it was not considered a clean direct-PR-first repository.

---

## Prompt 12 — Final Repository Choice

Exact prompt used:

```text
I think i will choose jgrapht afterall
```

Assistant response outcome:
JGraphT was accepted as the final chosen repository, with the warning that it should ideally be handled as a discussion-first repo. The suggested safe scope was to work only in `jgrapht-core` and avoid public API changes, algorithm rewrites, or broad architecture changes.

---

## Prompt 13 — Planning to Submit PR Without Asking First

Exact prompt used:

```text
i am planning to submit a pr only without asking kinda...
```

Assistant response outcome:
The response advised that submitting directly is riskier because JGraphT recommends discussing ideas first, but the risk can be reduced by making the PR very small, local, behavior-preserving, and respectful. The PR description should acknowledge the contribution guide and say the PR can be closed if maintainers prefer discussion first.

---

## Prompt 14 — Final Refactoring Plan

Exact prompt used:

```text
i am just gonna change one function of a class and break its long method down into smaller parts
```

Assistant response outcome:
This was confirmed as the least risky PR strategy. The chosen refactoring plan is to identify one Long Method smell in one JGraphT class and apply Extract Method by breaking the long method into smaller private helper methods. The change should preserve behavior, avoid public API changes, touch only one class, and be validated with relevant tests.

Final selected repository:
`jgrapht/jgrapht`

Final planned refactoring type:
Long Method → Extract Method

Final PR scope:
One function in one class, split into smaller private helper methods, with no algorithm behavior or public API change.

---

# Repository Names Checked During Selection

The following repository names were checked or discussed during the repository-selection process.

## Initial Java open-source shortlist

- `OpenFeign/feign`
- `TooTallNate/Java-WebSocket`
- `auth0/java-jwt`
- `dropwizard/metrics`
- `EsotericSoftware/kryo`
- `LMAX-Exchange/disruptor`
- `mapstruct/mapstruct`
- `ben-manes/caffeine`
- `locationtech/jts`
- `stripe/stripe-java`
- `mwiede/jsch`
- `eclipse-paho/paho.mqtt.java`
- `apache/commons-dbcp`
- `scribejava/scribejava`
- `brettwooldridge/HikariCP`
- `datafaker-net/datafaker`
- `google/Truth`
- `vavr-io/vavr`
- `square/javapoet`
- `uber/NullAway`
- `amaembo/streamex`
- `JCTools/JCTools`
- `apache/commons-configuration`
- `LMAX-Exchange/disruptor-proxy`
- `datafaker-net/datafaker-gen`
- `OpenFeign/feign-form`
- `google/error-prone`
- `vavr-io/vavr-jackson`
- `raphw/byte-buddy`
- `jboss-javassist/javassist`
- `cglib/cglib`
- `cache2k/cache2k`
- `ehcache/ehcache3`
- `infinispan/infinispan`
- `lessthanoptimal/BoofCV`
- `bytedeco/javacv`
- `immutables/immutables`
- `projectlombok/lombok`
- `jooby-project/jooby`
- `perwendel/spark`
- `eclipse-vertx/vert.x`
- `google/j2cl`
- `protocolbuffers/protobuf`
- `JMapperFramework/JMapper-core`
- `modelmapper/modelmapper`
- `orika-mapper/orika`
- `xebia-france/selma`
- `inferred/FreeBuilder`
- `JodaOrg/joda-beans`
- `ben-manes/concurrentlinkedhashmap`
- `qos-ch/slf4j`
- `square/okio`
- `FasterXML/jackson-databind`
- `apache/httpcomponents`
- `google/guice`
- `mybatis/mybatis-3`
- `netty/netty`
- `ReactiveX/RxJava`
- `square/retrofit`
- `square/okhttp`
- `google/guava`
- `jwgmeligmeyling/checkstyle-action`
- `chkal/gitlab-plugin`
- `patrickfav/checkstyle-config`
- `openrewrite/rewrite-maven`
- `krahets/hello-algo`

## Additional repositories from uploaded repository lists

- `remkop/picocli`
- `stleary/JSON-java`
- `jhy/jsoup`
- `awaitility/awaitility`
- `commonmark/commonmark-java`
- `google/gson`
- `drewnoakes/metadata-extractor`
- `jbangdev/jbang`
- `joelittlejohn/jsonschema2pojo`
- `jline/jline3`
- `javaparser/javaparser`
- `JSQLParser/JSqlParser`
- `TNG/ArchUnit`
- `LibrePDF/OpenPDF`
- `apache/pdfbox`
- `h2database/h2database`
- `jgrapht/jgrapht`
- `testcontainers/testcontainers-java`
- `wiremock/wiremock`
- `checkstyle/checkstyle`
- `mockito/mockito`
- `junit-team/junit-framework`
- `assertj/assertj`
- `hamcrest/JavaHamcrest`
- `jqwik-team/jqwik`
- `FasterXML/jackson-core`
- `FasterXML/jackson-dataformat-xml`
- `rest-assured/rest-assured`
- `NanoHttpd/nanohttpd`
- `optimatika/ojAlgo`
- `classgraph/classgraph`
- `vsch/flexmark-java`
- `zxing/zxing`
- `haraldk/TwelveMonkeys`
- `rometools/rome`
- `bucket4j/bucket4j`
- `bbottema/simple-java-mail`
- `google/jimfs`
- `pmd/pmd`
- `jankotek/mapdb`
- `skyscreamer/JSONassert`
- `jwtk/jjwt`
- `apache/commons-lang`
- `apache/commons-io`
- `danfickle/openhtmltopdf`
- `jqno/equalsverifier`
- `srikanth-lingala/zip4j`
- `ronmamo/reflections`
- `lz4/lz4-java`
- `apache/commons-collections`

## Smaller or easier candidates considered later

- `jmrozanec/cron-utils`
- `zafarkhaja/java-semver`
- `coobird/thumbnailator`
- `wnameless/json-flattener`
- `cdimascio/dotenv-java`
- `knowm/XChart`
- `kagkarlsson/db-scheduler`
- `argparse4j/argparse4j`
- `failsafe-lib/failsafe`
- `tinylog-org/tinylog`
- `seancfoley/IPAddress`
- `EsotericSoftware/yamlbeans`
- `fusesource/jansi`
- `greenmail-mail-test/greenmail`
- `jknack/handlebars.java`
- `j-easy/easy-random`
- `univocity/univocity-parsers`
- `j-easy/easy-rules`
- `osiegmar/FastCSV`
- `Incendo/cloud`
- `cbeust/jcommander`
- `lookfirst/sardine`
- `AlmasB/FXGL`
- `javalin/javalin`
- `junit-team/junit4`
- `opensearch-project/OpenSearch`
- `jenkinsci/jenkins`
- `termux/termux-app`
- `elastic/elasticsearch`
- `Anuken/Mindustry`
- `SeleniumHQ/selenium`
- `OpenAPITools/openapi-generator`
- `cabaletta/baritone`
- `kunal-kushwaha/DSA-Bootcamp`
- `woheller69/FreeDroidWarn`
- `apache/zookeeper`
- `apache/hadoop`
- `j-easy/easy-batch`
- `j-easy/easy-flows`

---

# Final Decision

Final selected repository:

```text
jgrapht/jgrapht
```

Final reason:
After checking many repositories, the chosen project was JGraphT. It is contribution-friendly but discussion-first. The planned PR is intentionally small and behavior-preserving to reduce risk.

Final refactoring plan:

```text
Refactor one Long Method in one JGraphT class by extracting smaller private helper methods.
```

Final safe scope:

```text
One function in one class.
No public API change.
No algorithm behavior change.
No broad formatting-only cleanup.
No multi-class architecture rewrite.
```
