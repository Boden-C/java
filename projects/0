    import java.io.BufferedReader;
    import java.io.BufferedWriter;
    import java.io.File;
    import java.io.FileReader;
    import java.io.FileWriter;
    import java.io.IOException;
    import java.util.Scanner;

    class Main {
        static final String OUTPUT_FILE_NAME = "A1.txt";
        static final int MAX_ROWS = 10;
        static final int MAX_COL = 26;
        static final double ADULT_PRICE = 10.0;
        static final double CHILD_PRICE = 5.0;
        static final double SENIOR_PRICE = 7.5;

        public static void main(String[] args) {

            Scanner scanner = new Scanner(System.in);
            char[][] auditorium = new char[MAX_ROWS][MAX_COL];
            int rows = 0;
            int col = 0;

            // Get and read file
            boolean invalidFile = true;
            while (invalidFile) {
                System.out.print("Enter a valid file name: ");
                String fileName = scanner.nextLine();
                File file = new File(fileName);

                // Read file
                try {
                    if (file.exists() && file.isFile() && file.getName().endsWith(".txt")) {
                        char[][] fileAuditorium = new char[MAX_ROWS][MAX_COL];
                        int fileSeats = -1; // Invalid value

                        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
                            String line;
                            int currentRow = 0;
                            while ((line = reader.readLine()) != null) {
                                // Check seats in row
                                if (fileSeats == -1) {
                                    fileSeats = line.length();
                                } else if (line.length() != fileSeats) {
                                    throw new IllegalArgumentException("Invalid format, different row lengths");
                                }
                                for (int currentCol = 0; currentCol < line.length(); currentCol++) {
                                    char seat = line.charAt(currentCol);
                                    if (seat == 'A' || seat == 'C' || seat == 'S' || seat == '.') {
                                        fileAuditorium[currentRow][currentCol] = seat;
                                    } else {
                                        throw new IllegalArgumentException(
                                                "Invalid format, unrecognized character: " + seat);
                                    }
                                }
                                currentRow++;
                            }
                            auditorium = fileAuditorium;
                            rows = currentRow;
                            col = fileSeats;
                            invalidFile = false;
                        }
                    } else {
                        throw new IllegalArgumentException("Invalid file");
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                } catch (IllegalArgumentException e) {
                    System.out.println(e.getMessage());
                }
            }

            // Main Menu
            boolean exit = false;
            while (!exit) {
                System.out.println("1. Reserve Seats");
                System.out.println("2. Exit");
                System.out.print("Choose option number: ");
                char choice = scanner.next().charAt(0);

                switch (choice) {
                    case '1':
                        // Reserve Seats
                        displayAuditorium(auditorium, rows, col);

                        int userRow = 0;
                        int userCol = 0;
                        int numAdult = 0;
                        int numChild = 0;
                        int numSenior = 0;

                        userRow = validateInputInt("Row number: ", scanner, 1, rows) - 1;
                        userCol = validateInputChar("Starting seat letter: ", scanner, 1, col) - 1;
                        numAdult = validateInputInt("Number of adult tickets: ", scanner, 0, -1);
                        numChild = validateInputInt("Number of child tickets: ", scanner, 0, -1);
                        numSenior = validateInputInt("Number of senior tickets: ", scanner, 0, -1);

                        int totalSeats = numAdult + numChild + numSenior;

                        if (totalSeats == 0) {
                            System.out.println("no seats reserved");
                            break;
                        }

                        if (isAvailable(auditorium[userRow], userCol, totalSeats)) {
                            reserveSeats(auditorium[userRow], userCol, numAdult, numChild, numSenior);
                            break;
                        }

                        int seatIndex = findBestSeats(auditorium[userRow], col, totalSeats);

                        if (seatIndex == -1) {
                            System.out.println("no seats available");
                            break;
                        }

                        System.out.println("Unavailable. Best available seats: " + 
                                        (userRow + 1) + (char) ('A' + seatIndex) + " - "
                                        + (userRow + 1) + (char) ('A' + seatIndex + totalSeats - 1));
                        if (validateInputConfirm("Reserve best available? (Y/N): ", scanner)) {
                            reserveSeats(auditorium[userRow], seatIndex, numAdult, numChild, numSenior);
                            break;
                        } else {
                            System.out.println("Reservation cancelled");
                            break;
                        }
                    case '2':
                        exit = true;
                        break;
                    default:
                        break;
                }
            }

            scanner.close();

            writeFile(auditorium, rows, col);
            displayReport(auditorium, rows, col);
        }

        private static void displayAuditorium(char[][] auditorium, int rows, int col) {
            // Header
            System.out.print("  ");
            for (int i = 0; i < col; i++) {
                System.out.print((char) ('A' + i));
            }
            System.out.println();

            // Body
            for (int i = 0; i < rows; i++) {
                System.out.print((i + 1));

                if (i < 9) {
                    System.out.print(" ");
                }
                for (int j = 0; j < col; j++) {
                    if (auditorium[i][j] != '.') {
                        System.out.print('#');
                    } else {
                        System.out.print('.');
                    }
                }
                System.out.println();
            }
        }

        private static void displayReport(char[][] auditorium, int rows, int col) {
            int totalSeats = rows * col;
            int totalAdultTickets = 0;
            int totalChildTickets = 0;
            int totalSeniorTickets = 0;
            double totalSales = 0.0;

            for (int i = 0; i < rows; i++) {
                for (int j = 0; j < col; j++) {
                    char seat = auditorium[i][j];
                    switch (seat) {
                        case 'A':
                            totalAdultTickets++;
                            totalSales += ADULT_PRICE;
                            break;
                        case 'C':
                            totalChildTickets++;
                            totalSales += CHILD_PRICE;
                            break;
                        case 'S':
                            totalSeniorTickets++;
                            totalSales += SENIOR_PRICE;
                            break;
                        default:
                            break;
                    }
                }
            }
            System.out.println("Total Seats: " + totalSeats);
            System.out.println("Total Tickets: " + (totalAdultTickets + totalChildTickets + totalSeniorTickets));
            System.out.println("Adult Tickets: " + totalAdultTickets);
            System.out.println("Child Tickets: " + totalChildTickets);
            System.out.println("Senior Tickets: " + totalSeniorTickets);
            System.out.println("Total Sales: $" + String.format("%.2f", totalSales));
        }

        private static void writeFile(char[][] auditorium, int rows, int col) {
            try (BufferedWriter writer = new BufferedWriter(new FileWriter(OUTPUT_FILE_NAME))) {
                for (int i = 0; i < rows; i++) {
                    for (int j = 0; j < col; j++) {
                        writer.write(auditorium[i][j]);
                    }
                    if (i < rows - 1) {
                        writer.newLine();
                    }
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        public static void reserveSeats(char[] row, int startingIndex, int a, int c, int s) {
            for (int i = startingIndex; i < row.length; i++) {
                if (a > 0) {
                    row[i] = 'A';
                    a--;
                    continue;
                } else if (c > 0) {
                    row[i] = 'C';
                    c--;
                    continue;
                } else if (s > 0) {
                    row[i] = 'S';
                    s--;
                    continue;
                }
                break;
            }
            System.out.println("Confirmation: Seats reserved");
        }

        private static int validateInputInt(String msg, Scanner scanner, int lowerBound, int upperBound) {
            int userInt = 0;
            while (true) {
                System.out.print(msg);
                if (scanner.hasNextInt()) {
                    userInt = scanner.nextInt();
                    if (userInt >= lowerBound && (userInt <= upperBound || upperBound == -1)) {
                        return userInt;
                    }
                } else if (scanner.hasNext()) {
                    scanner.next();
                }
                scanner.nextLine();
                System.out.println("Invalid input");
            }
        }

        private static int validateInputChar(String msg, Scanner scanner, int lowerBound, int upperBound) {
            char userChar;
            int userInt = 0;
            while (true) {
                System.out.print(msg);
                if (scanner.hasNext()) {
                    userChar = scanner.next().charAt(0);
                    if ('a' <= userChar && userChar <= 'z') {
                        userInt = userChar - 'a' + 1;
                        if (userInt >= lowerBound && userInt <= upperBound) {
                            return userInt;
                        }
                    } else if ('A' <= userChar && userChar <= 'Z') {
                        userInt = userChar - 'A' + 1;
                        if (userInt >= lowerBound && userInt <= upperBound) {
                            return userInt;
                        }
                    }
                }
                scanner.nextLine();
                System.out.println("Invalid input");
            }
        }

        private static boolean validateInputConfirm(String msg, Scanner scanner) {
            char userChar;
            while (true) {
                System.out.print(msg);
                if (scanner.hasNext()) {
                    userChar = scanner.next().charAt(0);
                    if (userChar == 'Y' || userChar == 'y') {
                        return true;
                    } else if (userChar == 'N' || userChar == 'n') {
                        return false;
                    }
                }
                scanner.nextLine();
                System.out.println("Invalid input");
            }
        }

        private static int findBestSeats(char[] seats, int numSeats, int n) {
            int bestDistance = -1;
            int bestSeatIndex = -1;

            for (int i = 0; i <= numSeats - n; i++) {
                if (isAvailable(seats, i, n)) {
                    int distance = Math.abs((i + (n - 1) / 2) - (numSeats - 1) / 2);
                    if (distance < bestDistance || bestDistance == -1) {
                        bestDistance = distance;
                        bestSeatIndex = i;
                        if (bestDistance < 1) { //Added quick exit
                            return bestSeatIndex;
                        }
                    }
                }
            }
            return bestSeatIndex;
        }

        private static boolean isAvailable(char[] seats, int index, int n) {
            boolean allEmpty = true;
            for (int i = 0; i < n; i++) {
                char seat = seats[index + i];
                if (seat != '.') {
                    allEmpty = false;
                    break;
                }
            }
            return allEmpty;
        }

    }
