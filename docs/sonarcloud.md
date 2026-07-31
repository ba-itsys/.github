# SonarCloud

We use SonarCloud for code quality analysis.

It is available at: https://sonarcloud.io/organizations/ba-itsys/projects

## User Access

Use your GitHub Account to log into https://sonarcloud.io/login

## Add a new user

Users are managed manually.

1. Each user has to log in at least once to create an account.
    - "Login with GitHub" at https://sonarcloud.io/login
2. Next goto https://sonarcloud.io/organizations/ba-itsys/members
3. Add the user by their email address.
4. If applicable, assign the role `Owner`.

## How to set up a new repo

1. Goto https://sonarcloud.io/projects/create?organization=ba-itsys
2. Select the repo and set it up
    - Note: The SonarCloud Org is connected to the GitHub Org `ba-itsys`.
    - This may take a while.
3. Next configure the CI under `Administration` > `Analysis method`
    1. Select `With GitHub Actions`
    2. **Important**: Use the globally configured SONAR_TOKEN. Do not generate a new one.
    3. Follow the instructions

## Offboarding

Remove the user from the project at https://sonarcloud.io/organizations/ba-itsys/members
