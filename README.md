# Android CI/CD pipline:

This pipeline orchestrates continuous integration (CI) and continuous deployment (CD) processes for an Android application using GitHub Actions and Firebase App Distribution. It automates code linting and unit testing, when changes are pushed to the **dev** branch, in addition to building and distribution of the Android app whenever a **pull request** is made to the **master** branch.


# Continuous Integration (CI) Workflow

This workflow ensures continuous integration by triggering the **Lint** and Test **jobs** whenever a push to `dev` branch is made.


## Jobs

### Lint

This job is responsible for running code linting and obtain lint report link.

#### Triggers

- **Event**: Push
- **Branches**: dev

#### Steps

1. **Code Checkout**: Checks out the code from the repository.
    ```yaml
    - name: Code Checkout
      uses: actions/checkout@v4
    ```

2. **Set up Java JDK 17**: Sets up Java Development Kit (JDK) version 17.
    ```yaml
    - name: Set up Java JDK 17
      uses: actions/setup-java@v1
      with:
        java-version: '17'
    ```

3. **Setup Gradle**: Sets up Gradle build tool.
    ```yaml
    - name: Setup Gradle
      uses: gradle/actions/setup-gradle@v3
    ```

4. **Grant execution permission for Gradle**: Grants execution permission for Gradle wrapper.
    ```yaml
    - name: Grant execution permission for Gradle
      run: chmod +x ./gradlew
    ```

5. **Run lint**: Performs linting
    ```yaml
    - name: Code Quality Checks
      run: ./gradlew lint
    ```
6. **Upload Lint Report**: 
    ```yaml
    - name: Upload lint report
      uses: actions/upload-artifact@v4
      with:
        name: lint.html
        path: app/build/reports/lint-results-debug.html
        overwrite: true
    ```

### test

This job is responsible for running unit tests on the Android app.

#### Triggers

- **Event**: Push
- **Branches**: dev

#### Steps

1. **Code Checkout**: Checks out the code from the repository.
    ```yaml
    - name: Code Checkout
      uses: actions/checkout@v4
    ```

2. **Set up Java JDK 17**: Sets up Java Development Kit (JDK) version 17.
    ```yaml
    - name: Set up Java JDK 17
      uses: actions/setup-java@v1
      with:
        java-version: '17'
    ```

3. **Setup Gradle**: Sets up Gradle build tool.
    ```yaml
    - name: Setup Gradle
      uses: gradle/actions/setup-gradle@v3
    ```

4. **Grant execution permission for Gradle**: Grants execution permission for Gradle wrapper.
    ```yaml
    - name: Grant execution permission for Gradle
      run: chmod +x ./gradlew
    ```

5. **Run Tests**: Executes tests for the Android app.
    ```yaml
    - name: Run Tests
      run: ./gradlew test
    ```

6. **Upload Test Report**: 
    ```yaml
    - name: Upload test report
      uses: actions/upload-artifact@v4
      with:
        name: Unit Test Report
        path: app/build/reports/test/testDebugUnitTest/
    ```

---

**Note**: This workflow ensures that tests pass and code quality standards are met before merging changes into the `master` branch. <br /><br />

# Continuous Deployment (CD) Workflow

This workflow ensures continuous deployment by triggering the deploy job whenever a pull request to `master` branch is made.

## Jobs

### deploy

This job will build a debug version of the Android app and deliver it to Firebase App Distribution.

#### Triggers

- **Event**: Pull Request
- **Branches**: master

#### Steps

1. **Code Checkout**: Checks out the code from the repository.
    ```yaml
    - name: Code Checkout
      uses: actions/checkout@v4
    ```

2. **Set up Java JDK 17**: Sets up Java Development Kit (JDK) version 17.
    ```yaml
    - name: Set up Java JDK 17
      uses: actions/setup-java@v1
      with:
        java-version: '17'
    ```

3. **Setup Gradle**: Sets up Gradle build tool.
    ```yaml
    - name: Setup Gradle
      uses: gradle/actions/setup-gradle@v3
    ```

4. **Grant execution permission for Gradle**: Grants execution permission for Gradle wrapper.
    ```yaml
    - name: Grant execution permission for Gradle
      run: chmod +x ./gradlew
    ```

5. **Build Debug**: Builds the debug version of the Android app.
    ```yaml
    - name: build debug
      run: ./gradlew assembleDebug
    ```

6. **Upload Artifact to Firebase App Distribution**: Uploads the app artifact to Firebase App Distribution.
    ```yaml
    - name: upload artifact to Firebase App Distribution
      uses: wzieba/Firebase-Distribution-Github-Action@v1
      with:
        appId: ${{secrets.FIREBASE_APP_ID}}
        serviceCredentialsFileContent: ${{ secrets.CREDENTIAL_FILE_CONTENT }}
        groups: testers
        file: app/build/outputs/apk/debug/app-debug.apk
    ```

---

**Note**: Make sure to configure the required secrets (`FIREBASE_APP_ID` and `CREDENTIAL_FILE_CONTENT`) in your repository settings to enable the Firebase upload functionality.
