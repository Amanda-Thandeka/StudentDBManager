

package tudentdbmanager;
package za.ac.tut.bl;

import java.util.List;
import za.ac.tut.bl.DAO;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.ResultSet;
import java.sql.PreparedStatement;
import java.util.ArrayList;
import za.ac.tut.entity.Student;

/**
 * This class handles all database operations for Student objects.
 *
 * It connects to a database using JDBC and allows adding, updating,
 * deleting, and retrieving student records.
 */
public class StudentManager implements DAO<Student> {

    private Connection connection;

    /**
     * Creates a database connection with the provided details.
     *
     * @param dbURL    the database connection URL
     * @param username the database username
     * @param password the database password
     * @throws SQLException if the connection fails
     */
    public StudentManager(String dbURL, String username, String password) throws SQLException {
        connection = getConnection(dbURL, username, password);
    }

    /**
     * Adds a new student to the database.
     *
     * @param student the student to add
     * @return true if added successfully
     */
    @Override
    public Boolean add(Student student) {
        String sql = "INSERT INTO StudentTBL (StudentNumber, Name, Surname) VALUES (?,?,?)";

        try {
            PreparedStatement ps = connection.prepareStatement(sql);
            ps.setInt(1, student.getStudentNumber());
            ps.setString(2, student.getName());
            ps.setString(3, student.getSurname());
            ps.executeUpdate();
            return true;
        } catch (SQLException ex) {
            System.err.println(ex);
            return false;
        }
    }

    /**
     * Updates an existing student's details.
     *
     * @param student the student with updated information
     * @return true if update succeeded
     */
    @Override
    public Boolean update(Student student) {
        String sql = "UPDATE StudentTBL SET Name = ?, Surname = ? WHERE StudentNumber = ?";

        try {
            PreparedStatement ps = connection.prepareStatement(sql);
            ps.setString(1, student.getName());
            ps.setString(2, student.getSurname());
            ps.setInt(3, student.getStudentNumber());
            ps.executeUpdate();
            return true;
        } catch (SQLException ex) {
            System.err.println(ex);
            return false;
        }
    }

    /**
     * Deletes a student from the database using their student number.
     *
     * @param student the student to delete
     * @return true if deletion succeeded
     */
    @Override
    public Boolean delete(Student student) {
        String sql = "DELETE FROM StudentTBL WHERE StudentNumber = ?";

        try {
            PreparedStatement ps = connection.prepareStatement(sql);
            ps.setInt(1, student.getStudentNumber());
            ps.executeUpdate();
            return true;
        } catch (SQLException ex) {
            System.err.println(ex);
            return false;
        }
    }

    /**
     * Retrieves a student by their student number.
     *
     * @param studentNumber the student number
     * @return the Student object if found, otherwise null
     */
    @Override
    public Student get(Integer studentNumber) {
        String sql = "SELECT StudentNumber, Name, Surname FROM StudentTBL WHERE StudentNumber = ?";

        try {
            PreparedStatement ps = connection.prepareStatement(sql);
            ps.setInt(1, studentNumber);
            ResultSet rs = ps.executeQuery();

            if (rs.next()) {
                return new Student(
                        rs.getInt("StudentNumber"),
                        rs.getString("Name"),
                        rs.getString("Surname")
                );
            }
            return null;
        } catch (SQLException ex) {
            System.err.println(ex);
            return null;
        }
    }

    /**
     * Retrieves all students from the database.
     *
     * @return a list of all students
     */
    @Override
    public List<Student> getAll() {
        String sql = "SELECT * FROM StudentTBL";
        List<Student> students = new ArrayList<>();

        try {
            PreparedStatement ps = connection.prepareStatement(sql);
            ResultSet rs = ps.executeQuery();

            while (rs.next()) {
                students.add(new Student(
                        rs.getInt("StudentNumber"),
                        rs.getString("Name"),
                        rs.getString("Surname")
                ));
            }
            return students;
        } catch (SQLException ex) {
            System.err.println(ex);
            return null;
        }
    }

    /**
     * Opens a connection to the database.
     */
    private Connection getConnection(String dbURL, String username, String password) throws SQLException {
        return DriverManager.getConnection(dbURL, username, password);
    }
}


