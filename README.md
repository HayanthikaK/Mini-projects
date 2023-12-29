# Mini-projects
import java.sql.*;
import java.util.Scanner;

public class AttendanceManagementSystem {

    private static final String DATABASE_URL = "jdbc:sqlite:attendance.db";
    
    public static void main(String[] args) {
        initializeDatabase();
        
        Scanner scanner = new Scanner(System.in);
        
        while (true) {
            System.out.println("\nAttendance Management System");
            System.out.println("1. Mark Attendance");
            System.out.println("2. Display Attendance");
            System.out.println("3. Exit");
            System.out.print("Enter your choice: ");

            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            switch (choice) {
                case 1:
                    System.out.print("Enter student ID: ");
                    String studentId = scanner.nextLine();
                    markAttendance(studentId);
                    break;
                case 2:
                    displayAttendance();
                    break;
                case 3:
                    System.out.println("Exiting the program. Goodbye!");
                    System.exit(0);
                default:
                    System.out.println("Invalid choice. Please enter a valid option.");
            }
        }
    }

    private static void initializeDatabase() {
        try (Connection connection = DriverManager.getConnection(DATABASE_URL);
             Statement statement = connection.createStatement()) {

            // Create the 'attendance' table if not exists
            String createTableQuery = "CREATE TABLE IF NOT EXISTS attendance ("
                    + "id INTEGER PRIMARY KEY AUTOINCREMENT,"
                    + "student_id TEXT NOT NULL,"
                    + "is_present BOOLEAN NOT NULL,"
                    + "timestamp DATETIME DEFAULT CURRENT_TIMESTAMP)";
            statement.executeUpdate(createTableQuery);

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private static void markAttendance(String studentId) {
        try (Connection connection = DriverManager.getConnection(DATABASE_URL);
             PreparedStatement preparedStatement = connection.prepareStatement(
                     "INSERT INTO attendance (student_id, is_present) VALUES (?, ?)")) {

            preparedStatement.setString(1, studentId);
            preparedStatement.setBoolean(2, true);

            int rowsAffected = preparedStatement.executeUpdate();

            if (rowsAffected > 0) {
                System.out.println("Attendance marked for student ID: " + studentId);
            } else {
                System.out.println("Failed to mark attendance. Please try again.");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private static void displayAttendance() {
        try (Connection connection = DriverManager.getConnection(DATABASE_URL);
             Statement statement = connection.createStatement();
             ResultSet resultSet = statement.executeQuery("SELECT * FROM attendance")) {

            System.out.println("Attendance Records:");
            while (resultSet.next()) {
                int id = resultSet.getInt("id");
                String studentId = resultSet.getString("student_id");
                boolean isPresent = resultSet.getBoolean("is_present");
                String timestamp = resultSet.getString("timestamp");

                System.out.println("ID: " + id + ", Student ID: " + studentId
                        + ", Present: " + isPresent + ", Timestamp: " + timestamp);
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}




