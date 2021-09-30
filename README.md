Large-scale Evidence Generation and Evaluation across a Network of Databases for Type 2 Diabetes Mellitus (LEGEND-T2DM)
=============================================================================

<img src="https://img.shields.io/badge/Study%20Status-Results%20Available-yellow.svg" alt="Study Status: Results Available">

- Analytics use case(s): **Population-Level Estimation**
- Study type: **Clinical Application**
- Tags: **-**
- Study lead: **Marc A. Suchard**
- Study lead forums tag: **[msuchard](https://forums.ohdsi.org/u/msuchard)**
- Study start date: **-**
- Study end date: **-**
- Protocol: **[HTML document](https://ohdsi-studies.github.io/LegendT2dm/Protocol.html)**
- Publications: **-**
- Results explorer: **-**

Requirements
============

- A database in [Common Data Model version 5](https://github.com/OHDSI/CommonDataModel) in one of these platforms: SQL Server, Oracle, PostgreSQL, IBM Netezza, Apache Impala, Amazon RedShift, Google BigQuery, or Microsoft APS.
- R version 4.0.3 or newer
- On Windows: [RTools](http://cran.r-project.org/bin/windows/Rtools/)
- [Java](http://java.com)
- 100 GB of free disk space

How to run
==========
1. Follow [these instructions](https://ohdsi.github.io/Hades/rSetup.html) for seting up your R environment, including RTools and Java.

2. Open your study package in RStudio. Use the following code to install all the dependencies:

	```r
	renv::active()
	renv::restore()
	```

3. In RStudio, select 'Build' then 'Install and Restart' to build the package.

3. Once installed, you can execute the feasibility assessment partion of the study by modifying and using the code below. For your convenience, this code is also provided under `extras/CodeToRun.R`:

	```r
	library(LegendT2dm)
	
	Sys.setenv(DATABASECONNECTOR_JAR_FOLDER="s:/DatabaseDrivers")
	
	# Run-once: set-up your database driver
	DatabaseConnector::downloadJdbcDrivers(dbms = "postgresql")
	
	# Optional: specify where the temporary files (used by the Andromeda package) will be created:
	options(andromedaTempFolder = "s:/AndromedaTemp")

	# Maximum number of cores to be used:
	maxCores <- min(4, parallel::detectCores()) # Or more depending on your hardware

	# Minimum cell count when exporting data:
	minCellCount <- 5

	# The folder where the study intermediate and result files will be written:
	outputFolder <- "s:/LegendT2dmStudy"

	# Details for connecting to the server:
	# See ?DatabaseConnector::createConnectionDetails for help
	connectionDetails <- DatabaseConnector::createConnectionDetails(dbms = "postgresql",
									server = "some.server.com/ohdsi",
									user = "joe",
									password = "secret")

	# The name of the database schema where the CDM data can be found:
	cdmDatabaseSchema <- "cdm_synpuf"

	# The name of the database schema and table where the study-specific cohorts will be instantiated:
	cohortDatabaseSchema <- "scratch.dbo"
	tablePrefix <- "legendt2dm_study"

	# Some meta-information that will be used by the export function:
	databaseId <- "Synpuf"
	databaseName <- "Medicare Claims Synthetic Public Use Files (SynPUFs)"
	databaseDescription <- "Medicare Claims Synthetic Public Use Files (SynPUFs) were created to allow interested parties to gain familiarity using Medicare claims data while protecting beneficiary privacy. These files are intended to promote development of software and applications that utilize files in this format, train researchers on the use and complexities of Centers for Medicare and Medicaid Services (CMS) claims, and support safe data mining innovations. The SynPUFs were created by combining randomized information from multiple unique beneficiaries and changing variable values. This randomization and combining of beneficiary information ensures privacy of health information."

	# For some database platforms (e.g. Oracle): define a schema that can be used to emulate temp tables:
	options(sqlRenderTempEmulationSchema = NULL)

	# Feasibility assessment ---------------------------------------------------------
	assessPhenotypes(connectionDetails = connectionDetails,
					 cdmDatabaseSchema = cdmDatabaseSchema,
					 oracleTempSchema = oracleTempSchema,
					 cohortDatabaseSchema = cohortDatabaseSchema,
					 outputFolder = outputFolder,
					 tablePrefix = tablePrefix,
					 databaseId = databaseId,
					 databaseName = databaseName,
					 databaseDescription = databaseDescription,
					 createExposureCohorts = TRUE,
					 runExposureCohortDiagnostics = TRUE,
					 createOutcomeCohorts = TRUE,
					 runOutcomeCohortDiagnostics = TRUE)

	```

4. Upload the files ```class/cohortDiagnosticsExport/Results_<DatabaseId>.zip``` and ```outcome/cohortDiagnosticsExport/Results_<DatabaseId>.zip``` in the output folder to the study coordinator:

	```r
	uploadFeasbilitityResults(outputFolder, privateKeyFileName = "<file>", userName = "<name>")
	```

    where `<file>` and `<name>` are the credentials provided to you personally by the study coordinator.

License
=======
The `LegendT2dm` package is licensed under Apache License 2.0

Development
===========
`LegendT2dm` was developed in ATLAS and R Studio.

### Development status

In development
