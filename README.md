<!-- @format -->

<p align="center">
    <a href="https://eclipse.github.io/steady/">
        <img height="64" src="docs/public/content/images/ES-logo-152-transparent.png">
    </a>
</p>

# Eclipse Steady (Incubator Project)

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE.txt)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Build Status](https://travis-ci.org/eclipse/steady.svg?branch=master)](https://travis-ci.org/eclipse/steady)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.sap.research.security.vulas/plugin-maven/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.sap.research.security.vulas/plugin-maven)
[![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/4202/badge)](https://bestpractices.coreinfrastructure.org/projects/4202)
[![REUSE status](https://api.reuse.software/badge/github.com/eclipse/steady)](https://api.reuse.software/info/github.com/eclipse/steady)

**Discover, assess and mitigate known vulnerabilities in your Java and Python projects**

In terms of the safe use of open-source components during application development, Eclipse Steady assists software development organizations. The method examines applications written in **Java** and **Python** in order to:

- Determine if they depend on open-source components that are considered to be vulnerable,
- gather proof of vulnerable code execution in a specific application context (using a mixture of static and dynamic analysis techniques), and
- assist developers in reducing the impact of such dependencies.

As such, it addresses the OWASP Top 10 security risk A9, [Using Components with Known Vulnerabilities](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities), which is often the root cause of data breaches: [snyk.io/blog/owasp-top-10-breaches](https://snyk.io/blog/owasp-top-10-breaches/)

In comparison to other tools, the detection is code-centric and usage-based, which allows for more accurate detection and assessment than tools relying on meta-data. It is a collection of client-side scan tools, microservices and rich [OpenUI5](https://openui5.hana.ondemand.com/) Web frontends.

## History

[Originally developed](https://scholar.google.com/citations?user=FOEVZyYAAAAJ&hl=en) by [SAP Security Research](https://www.sap.com/documents/2017/12/cc047065-e67c-0010-82c7-eda71af511fa.html) since late 2016, SAP has been using the method in a constructive manner (but an earlier prototype was available since 2015). In April 2017, the tool was designated as SAP's official open-source scan solution for Java (and later Python) applications. It has been used to conduct 1 million+ scans of 1000 Java and Python software projects as of April 2019, and its adoption is steadily increasing.

The tool's method is best outlined in the following scientific papers; if you use the tool in your study, please cite them:

- [Serena Ponta](https://scholar.google.com/citations?hl=en&user=DFVwF6sAAAAJ), [Henrik Plate](https://scholar.google.com/citations?user=Kaleo5YAAAAJ&hl=en), [Antonino Sabetta](https://scholar.google.com/citations?hl=en&user=BhcceV8AAAAJ), [**Detection, assessment and mitigation of vulnerabilities in open source dependencies**](https://link.springer.com/article/10.1007/s10664-020-09830-x), Empirical Software Engineering, volume 25, pages 3175–3215 (2020)
- [Serena Ponta](https://scholar.google.com/citations?hl=en&user=DFVwF6sAAAAJ), [Henrik Plate](https://scholar.google.com/citations?user=Kaleo5YAAAAJ&hl=en), [Antonino Sabetta](https://scholar.google.com/citations?hl=en&user=BhcceV8AAAAJ), [**Beyond Metadata: Code-centric and Usage-based Analysis of Known Vulnerabilities in Open-source Software**](https://arxiv.org/abs/1806.05893), 34th International Conference on Software Maintenance and Evolution (ICSME), 2018
- [Henrik Plate](https://scholar.google.com/citations?user=Kaleo5YAAAAJ&hl=en), [Serena Ponta](https://scholar.google.com/citations?hl=en&user=DFVwF6sAAAAJ), [Antonino Sabetta](https://scholar.google.com/citations?hl=en&user=BhcceV8AAAAJ), [**Impact Assessment for Vulnerabilities in Open-Source Software Libraries**](https://arxiv.org/pdf/1504.04971.pdf), 31st International Conference on Software Maintenance and Evolution (ICSME), 2015

## Features

- **Vulnerable code detection** is accomplished by looking for method signatures in Java repositories and comparing their source and byte code to the vulnerable and fixed versions (as known from the fix commit). As a result, the detection is more precise than approaches that depend on meta-data (less false-positives and false-negatives). It is particularly resistant to rebundling, which is a common practice in the Java ecosystem.
- **Assessment of vulnerable dependencies** is knowledge about the potential and actual execution of vulnerable code is used by application developers and security experts. This data is derived from call graph analysis and trace data gathered during JUnit and integration tests. Application developers are faced with the potential and real call stack from application code to vulnerable code, down to the granularity of single methods.
- Adding new vulnerabilities to the knowledge base does not necessitate a new application search. In other words, it is instantly known whether previously scanned applications are affected or not after an addition to the knowledge base.
- **Mitigation proposals** consider the _reachable_ share of dependencies, or the collection of methods that can be accessed from application code combined with actual executions observed during tests. This data is used to generate a number of metrics that help developers choose the best non-vulnerable dependency replacement (best in regards to non-breaking and with least regression likelihood).
- Individual results can be exempted if developers determine that a vulnerability cannot be exploited in the sense of a specific application. This information (including timestamps and author information) can be audited and usually prevents construct exceptions during CI/CD pipelines.
- Internal CERTs within an organization can search for all applications impacted by a specific vulnerability. For example, larger development organizations with a large number of software applications produced by distributed and decentralized development units may benefit from this function.

## Requirements

Eclipse Steady is built on a distributed architecture that includes a few Spring Boot microservices, two Web frontends, and a range of client-side scanners/plugins that analyze application and dependency code on build systems or developer workstations.

To build/test the entire project, the following tools are needed:

- **[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)**
- **[Maven 3.3+](https://maven.apache.org/download.cgi)** for the analysis of Maven projects using [`plugin-maven`](https://github.com/eclipse/steady/tree/master/plugin-maven)
- **[Python 3](https://www.python.org/downloads/)** as well as the packages `pip`, `virtualenv` and `setuptools` (`pip install -r requirements.txt`) for the analysis of Python applications using [`cli-scanner`](https://github.com/eclipse/steady/tree/master/cli-scanner)
- **[Gradle 4](https://gradle.org/install/)** for the analysis of Gradle projects using [`plugin-gradle`](https://github.com/eclipse/steady/tree/master/plugin-gradle).

## Build and Test

Eclipse Steady is built with Maven. To enable the support for Gradle the profile `gradle` needs to be activated (`-P gradle`)

```sh
mvn clean install
```

During the `install`ation phase of `mvn` all the tests are run. Long-running tests can be disabled with the flag `-DexcludedGroups=com.sap.psr.vulas.shared.categories.Slow`. All the tests can be disabled with the flag `-DskipTests`.

## Limitations

Due to the current lack of an authentication and authorization mechanism, it is NOT recommended to run the Web frontends and server-side microservices on systems accessible from the Internet.

Other limitations:

- Static and dynamic analyses are not implemented for Python
- Static analysis for Java is only supported until Java 8
- Java 9 multi-release archives are not supported (classes below `META-INF/versions` are simply ignored)

## Todo (upcoming changes)

The following is a subset of pending feature requests:

- Static and dynamic analysis for Python
- Support of JavaScript (client- and server-side)
- UI dashboards for workspaces

## Acknowledgement

This work is partly funded by the EU under the H2020 research project [SPARTA](https://sparta.eu/) (Grant No.830892).

[**Documentation**](https://eclipse.github.io/steady/user/) · [**Support**](https://eclipse.github.io/steady/user/support/) · [**Contributing**](https://eclipse.github.io/steady/contributor/) · [**Deploy guide**](https://eclipse.github.io/steady/admin/tutorials/docker/) · [**Scan guide**](https://eclipse.github.io/steady/user/tutorials/) · [**Vulnerability database**](https://eclipse.github.io/steady/vuln_db/) · [**Blog**](https://blogs.sap.com/tag/vulas/)
