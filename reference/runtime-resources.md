# Runtime Resources

Some labs require a running application. If your facilitator has provided hosted instance URLs, use those. Otherwise, you can ask Devin to run applications locally on its machine using the instructions below.

## Local Run Instructions

### timesheet-app
```bash
# Backend (port 3001)
cd ~/repos/timesheet-app/backend && npm run dev

# Frontend (port 5173)
cd ~/repos/timesheet-app/frontend && npm run dev

# Access: http://localhost:5173
# Login: any email address (no password required)
```

### calcom
```bash
cd ~/repos/calcom && yarn dev
# Access: http://localhost:3000
```

### ts-java-spring-boot-realworld (Labs 2 & 3)
```bash
cd ~/repos/ts-java-spring-boot-realworld
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export PATH="$JAVA_HOME/bin:$PATH"
./gradlew bootRun
# Access: http://localhost:8080
# Verify: http://localhost:8080/tags
```

## Labs Requiring a Running App

| Lab | Application Needed | Can Run Locally? |
|-----|-------------------|-----------------|
| E2E Testing | calcom or timesheet-app | Yes |
| Fix Runtime Bug | calcom | Yes |
| Fix UI Bug | timesheet-app | Yes |
| Fix Data Bug | timesheet-app | Yes (but code analysis also works) |
