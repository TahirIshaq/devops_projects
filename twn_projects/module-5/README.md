# Objective
1. Create a VM on any cloud provider.
2. Build a gradle project on the system.
3. Copy the build artifact to the VM.
4. Run the artifact on VM using java.

## Implementation
### VM Creation
Create a new VM on azure by follwing these [steps](../../practice_projects/project-2/README.md).

### Gradle
Docker image will be used to build a `Hello World` project.
```
docker pull gradle:jdk24-noble
```
Create gradle project directory
```
mkdir $PWD/twn_projects/module-5/gradle_project
```
Gradle follows a project structure. This can be created manually or using `gradle init` in both [interactive](https://docs.gradle.org/current/userguide/part1_gradle_init.html) or [non-interactive](https://docs.gradle.org/current/userguide/build_init_plugin.html) mode. More information about [non-interactive](https://discuss.gradle.org/t/is-there-any-way-to-do-non-interactive-mode/48546/3).

Here, we create a gradle project using non-interactive mode. Be default gradle create a hello world project.
```
docker run --rm -u gradle \
-v "$PWD"/twn_projects/module-5/gradle_project:/home/gradle/project \
-w /home/gradle/project \
gradle:jdk24-noble \
gradle init \
  --type java-application \
  --dsl kotlin \
  --test-framework junit-jupiter \
  --package org.example \
  --project-name hello_world  \
  --no-split-project  \
  --java-version 21 \
  --use-defaults
```

Update `gradle_project/app/build.gradle.kts` to make the `.jar` artifact executeable. The value of `Main-Class` should be the same as `package` value set in gradle init or whatever class is being used.
```
echo -e "\ntasks.withType<Jar> {\n\tmanifest {\n\t\tattributes[\"Main-Class\"] = \"org.example.App\"\n\t}\n}" | tee -a $PWD/twn_projects/module-5/gradle_project/app/build.gradle.kts
```

Update the project print value
```
sed -i 's/Hello World!/Hello World from Gradle!/' $PWD/twn_projects/module-5/gradle_project/app/src/main/java/org/example/App.java
```
Build the project
```
docker run --rm -u gradle \
-v "$PWD"/twn_projects/module-5/gradle_project:/home/gradle/project \
-w /home/gradle/project \
gradle:jdk24-noble gradle build
```

### Upload artificat to VM
The `.jar` will be in `$PWD/twn_projects/module-5/gradle_project/app/build/libs/app.jar`. Upload the file to VM using `scp` or `rsync` by following these [steps](../../practice_projects/project-2/README.md)

### Run the artifact
Login to the VM via `SSH` by following these [steps](../../practice_projects/project-2/README.md).

Download java docker image:
```
docker pull eclipse-temurin:25_36-jre-jammy
```

Run this file using jdk:
```
docker run --rm \
-v "$PWD"/jar_files/app.jar:/app/app.jar \
-w /app \
eclipse-temurin:25_36-jre-jammy \
java -jar app.jar
```

### Gradle Cleanup
Clean the project. This will remove all files created during `build` stage.
```
docker run --rm -u gradle \
-v "$PWD"/twn_projects/module-5/gradle_project:/home/gradle/project \
-w /home/gradle/project \
gradle:jdk24-noble gradle clean
```