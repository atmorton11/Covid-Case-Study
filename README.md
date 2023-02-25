# Covid-Case-Study
Analyzing the numbers of infections, deaths, and vaccinations across the world

--Select the data we will be using

SELECT location, date, total_cases, new_cases, total_deaths, population
FROM covid_deaths.covid_deaths
WHERE continent IS NOT NULL 
ORDER BY 1, 2
;

--Compare total cases to total deaths
--Shows rate of death by Covid in this country

SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 AS DeathPercentage
FROM covid_deaths.covid_deaths
--WHERE location = 'United States'
WHERE continent IS NOT NULL 
ORDER BY 1, 2 
;

--Look at total cases vs population
--Shows what percentage of population got infected

SELECT location, date, total_cases, population, (total_cases/population)*100 AS InfectionPercentage
FROM covid_deaths.covid_deaths
--WHERE location = 'United States'
WHERE continent IS NOT NULL 
;

SELECT location, date, total_cases, population, (total_cases/population)*100 AS InfectionPercentage
FROM covid_deaths.covid_deaths
WHERE location = 'United States'
;

--Look at countries with highest infection rate by population

SELECT location, population, MAX(total_cases) AS HighestInfectionCount, MAX((total_cases/population))*100 AS InfectionPercentage
FROM covid_deaths.covid_deaths
--WHERE location = 'United States' 
WHERE continent IS NOT NULL 
GROUP BY location, population
ORDER BY InfectionPercentage DESC
;

--Countries with highest death rate

SELECT location, MAX(cast(total_deaths AS INT)) AS TotalDeathCount,
FROM covid_deaths.covid_deaths
--WHERE location LIKE '%states%' 
WHERE continent IS NOT NULL 
GROUP BY location
ORDER BY TotalDeathCount DESC
;

--Break it down by continent
--Showing continents with highest death count per population

SELECT continent, MAX(cast(total_deaths AS INT)) AS TotalDeathCount,
FROM covid_deaths.covid_deaths
--WHERE location LIKE '%states%'
WHERE continent IS NOT NULL
GROUP BY continent
ORDER BY TotalDeathCount DESC
;

--Global Numbers

SELECT date, SUM(new_cases) AS totalcases, SUM(new_deaths) AS totaldeaths, SUM(new_deaths)/SUM(new_cases)*100 AS DeathPercentage--, total_cases, total_deaths 
FROM covid_deaths.covid_deaths
--WHERE location LIKE '%states%'
WHERE continent IS NOT NULL
GROUP BY date
ORDER BY 1, 2 
;

--Next, a look at the vaccinations stats

--Let's view our Covid vaccinations dataset

SELECT *
FROM `covid_deaths.covid_vaccinations`
;

--Join the two datasets

SELECT *
FROM `covid_deaths.covid_deaths` dea 
  JOIN covid_deaths.covid_vaccinations vacc
  ON dea.location = vacc.location 
  AND dea.date = vacc.date 
  ;
  
  --Total population vs vaccinations
SELECT dea.continent, dea.location, dea.date, dea.population, vacc.new_vaccinations, SUM(vacc.new_vaccinations) OVER (PARTITION BY dea.location ORDER BY dea.location, dea.date) AS RollingVaccCount
FROM `covid_deaths.covid_deaths` dea 
  JOIN covid_deaths.covid_vaccinations vacc
  ON dea.location = vacc.location 
  AND dea.date = vacc.date 
  WHERE dea.continent IS NOT NULL
  ORDER BY 2, 3
  ;
