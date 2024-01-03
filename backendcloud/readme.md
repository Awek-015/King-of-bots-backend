# Invictus Bots Configuration Guide

Dear Professor and TA, this project is an application based on microservice architecture, including multiple backend services and a frontend interface. Below are the steps to configure and start the entire application.

## Configuring the Database

1. First, we need to create the corresponding database, named 'kob'. I will provide a SQL file for creating tables, please run this SQL file in the database.
2. After creating the tables, please modify the settings in the `application.properties` file in the backend to configure your local database name and password.

## Running the Backend

Each backend service contains a Spring Boot application. To start these services, please follow the steps below:

1. Make sure your Java JDK version is 1.8.

2. Make sure Maven has loaded all dependencies. This step is crucial to ensure that all necessary libraries and frameworks required by the project are available.

3. Navigate to the `src/main/java` directory and find the main class with the `@SpringBootApplication` annotation.

4. Make sure to start the BackendApplication, BotRunningSystemApplication, and MatchingSystemApplication.

## Running the Frontend

1. Open a command line tool and navigate to the `web` directory of the project.
2. Execute the `npm install` command to install all required dependencies.
3. After completing the installation of dependencies, execute `npm run serve` to start the frontend service.

# Invictus Bots Operation Guide

## Login and Registration

After successfully starting the frontend and backend, open the frontend webpage to navigate to the login and registration interface. Please register an account before logging in.

## My Bot

Clicking on the username in the top right corner will display a dropdown menu. Here you can manage your Bot. Of course, if your Bot is empty, you can also play manually. Here I will provide a ready-made Bot for testing, please ensure the code is copied completely.

```java
package com.kob.botrunningsystem.utils;

import java.util.ArrayList;
import java.util.List;

public class Bot implements com.kob.botrunningsystem.utils.BotInterface {
    static class Cell {
        public int x, y;
        public Cell(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }

    private boolean check_tail_increasing(int step) { 
        if (step <= 10) return true;
        return step % 3 == 1;
    }

    public List<Cell> getCells(int sx, int sy, String steps) {
        steps = steps.substring(1, steps.length() - 1);
        List<Cell> res = new ArrayList<>();

        int[] dx = {-1, 0, 1, 0}, dy = {0, 1, 0, -1};
        int x = sx, y = sy;
        int step = 0;
        res.add(new Cell(x, y));
        for (int i = 0; i < steps.length(); i ++ ) {
            int d = steps.charAt(i) - '0';
            x += dx[d];
            y += dy[d];
            res.add(new Cell(x, y));
            if (!check_tail_increasing( ++ step)) {
                res.remove(0);
            }
        }
        return res;
    }

    @Override
    public Integer nextMove(String input) {
        String[] strs = input.split("#");
        int[][] g = new int[13][14];
        for (int i = 0, k = 0; i < 13; i ++ ) {
            for (int j = 0; j < 14; j ++, k ++ ) {
                if (strs[0].charAt(k) == '1') {
                    g[i][j] = 1;
                }
            }
        }

        int aSx = Integer.parseInt(strs[1]), aSy = Integer.parseInt(strs[2]);
        int bSx = Integer.parseInt(strs[4]), bSy = Integer.parseInt(strs[5]);

        List<Cell> aCells = getCells(aSx, aSy, strs[3]);
        List<Cell> bCells = getCells(bSx, bSy, strs[6]);

        for (Cell c: aCells) g[c.x][c.y] = 1;
        for (Cell c: bCells) g[c.x][c.y] = 1;

        int[] dx = {-1, 0, 1, 0}, dy = {0, 1, 0, -1};
        for (int i = 0; i < 4; i ++ ) {
            int x = aCells.get(aCells.size() - 1).x + dx[i];
            int y = aCells.get(aCells.size() - 1).y + dy[i];
            if (x >= 0 && x < 13 && y >= 0 && y < 14 && g[x][y] == 0) {
                return i;
            }
        }

        return 0;
    }
}
```

## Battle

1. You can create two users and log in at the same time, then click the Match button to start matching.
2. You can choose your own Bot or fight manually. If it's a bot battle, the snake will move according to the code; if you are controlling it yourself, you will use WASD to control the direction of the snake. You can only make one move per turn, and the snakes will move only after both sides have completed their actions.
3. After the battle ends, click on Game Lists to see all the battle records. Click on the Watch Record button to replay the battles.
4. Click on Rankings to view the battle points of all users. When matching, the system will prioritize opponents with similar points.