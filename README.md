## Release process

### Release the dependencies pom

Somewhere else, clone https://github.com/vert-x3/vertx-dependencies change its version and dependencies and deploy it

```
git clone https://githu.com/vert-x3/vertx-dependencies
cd vertx-dependencies
mvn versions:set -DnewVersion=3.0.0-milestoneX
# edit file and change stack.version to 3.0.0-milestoneX
mvn deploy -Psonatype-oss-release
git add .
git commit -m "Releasing 3.0.0-milestoneX"
git tag 3.0.0-milestoneX
```

### Clone released components

In this project execute

```
sh clone.sh
```

This creates a modules dir used by this project.

### Change components versions

Edit the root `pom.xml` and change the `vertx-dependencies` to `3.0.0-milestoneX` in the releaser plugin.

Now change the version of all modules with the releaser plugin

```
mvn io.vertx:releaser-maven-plugin:apply
```

### Deploy components

```
mvn deploy -Psonatype-oss-release -Dgpg.passphrase="my pass phrase" -DskipTests
```

Now this is staged in Nexus

### Do the release

When we are sure everything is ok (Nexus checked, components checked, ...)

Commit all components, this performs a `git commit` on all modules with the message _Releasing 3.0.0-milestoneX_

```
mvn io.vertx:releaser-maven-plugin:release
```

Then tag the release

```
mvn io.vertx:releaser-maven-plugin:tag
```

This adds the tag `3.0.0-milestoneX` to each module

### Now go back to snapshots

Edit the root `pom.xml` and change the `vertx-dependencies` to `3.0.0-SNAPSHOT` in the releaser plugin and perform

```
mvn io.vertx:releaser-maven-plugin:apply
mvn io.vertx:releaser-maven-plugin:release
mvn io.vertx:releaser-maven-plugin:tag
```

### Push everything back to their respective repos

```
sh push.sh
```

And also for vertx-dependencies of course.