import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.HashMap;

class HotelManagementSystemWithPayment extends JFrame {

    // Data storage
    private HashMap<String, Double> roomPrices = new HashMap<>(); // Room price storage
    private HashMap<String, String> bookings = new HashMap<>(); // Room to customer mapping

    // Constructor
    public HotelManagementSystemWithPayment() {
        // Frame setup
        setTitle("Hotel Management System with Billing & Payment");
        setSize(700, 500);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        // Title
        JLabel titleLabel = new JLabel("Hotel Management System with Billing & Payment", JLabel.CENTER);
        titleLabel.setFont(new Font("Arial", Font.BOLD, 20));
        add(titleLabel, BorderLayout.NORTH);

        // Tabs
        JTabbedPane tabs = new JTabbedPane();

        // Room Booking Tab
        JPanel bookingPanel = new JPanel(new GridLayout(5, 2, 10, 10));
        bookingPanel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        JLabel roomNumberLabel = new JLabel("Room Number:");
        JTextField roomNumberField = new JTextField();
        JLabel roomPriceLabel = new JLabel("Room Price (per day):");
        JTextField roomPriceField = new JTextField();
        JLabel customerNameLabel = new JLabel("Customer Name:");
        JTextField customerNameField = new JTextField();
        JButton bookButton = new JButton("Book Room");
        JTextArea bookingLog = new JTextArea(5, 20);
        bookingLog.setEditable(false);

        bookingPanel.add(roomNumberLabel);
        bookingPanel.add(roomNumberField);
        bookingPanel.add(roomPriceLabel);
        bookingPanel.add(roomPriceField);
        bookingPanel.add(customerNameLabel);
        bookingPanel.add(customerNameField);
        bookingPanel.add(new JLabel());
        bookingPanel.add(bookButton);
        bookingPanel.add(new JLabel("Booking Log:"));
        bookingPanel.add(new JScrollPane(bookingLog));

        tabs.add("Room Booking", bookingPanel);

        // Check-Out and Billing Tab
        JPanel billingPanel = new JPanel(new GridLayout(5, 2, 10, 10));
        billingPanel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        JLabel billingRoomLabel = new JLabel("Room Number:");
        JTextField billingRoomField = new JTextField();
        JButton checkOutButton = new JButton("Check-Out & Generate Bill");
        JTextArea billingLog = new JTextArea(5, 20);
        billingLog.setEditable(false);

        billingPanel.add(billingRoomLabel);
        billingPanel.add(billingRoomField);
        billingPanel.add(new JLabel());
        billingPanel.add(checkOutButton);
        billingPanel.add(new JLabel("Billing Log:"));
        billingPanel.add(new JScrollPane(billingLog));

        tabs.add("Billing", billingPanel);

        add(tabs, BorderLayout.CENTER);

        // Button Listeners
        bookButton.addActionListener(e -> {
            String room = roomNumberField.getText();
            String customer = customerNameField.getText();
            String priceText = roomPriceField.getText();
            if (!room.isEmpty() && !customer.isEmpty() && !priceText.isEmpty()) {
                try {
                    double price = Double.parseDouble(priceText);
                    roomPrices.put(room, price);
                    bookings.put(room, customer);
                    bookingLog.append("Room " + room + " booked by " + customer + " at $" + price + " per day.\n");
                    roomNumberField.setText("");
                    customerNameField.setText("");
                    roomPriceField.setText("");
                } catch (NumberFormatException ex) {
                    JOptionPane.showMessageDialog(this, "Invalid price entered!", "Error", JOptionPane.ERROR_MESSAGE);
                }
            } else {
                JOptionPane.showMessageDialog(this, "Please fill all fields!", "Error", JOptionPane.ERROR_MESSAGE);
            }
        });

        checkOutButton.addActionListener(e -> {
            String room = billingRoomField.getText();
            if (bookings.containsKey(room)) {
                String customer = bookings.get(room);
                double roomPrice = roomPrices.get(room);

                // Ask the number of days stayed
                String daysStayedText = JOptionPane.showInputDialog(this, "Enter the number of days stayed:");
                if (daysStayedText != null && !daysStayedText.isEmpty()) {
                    try {
                        int daysStayed = Integer.parseInt(daysStayedText);
                        double totalBill = roomPrice * daysStayed;

                        // Ask for payment details
                        String creditCardNumber = JOptionPane.showInputDialog(this, "Enter Credit Card Number:");
                        String password = JOptionPane.showInputDialog(this, "Enter Credit Card Password:");

                        if (creditCardNumber != null && password != null && !creditCardNumber.isEmpty() && !password.isEmpty()) {
                            billingLog.append("Room " + room + " checked out. Bill for " + customer + ": $" + totalBill + "\n");
                            billingLog.append("Payment successful for " + customer + " using card ending in " + creditCardNumber.substring(creditCardNumber.length() - 4) + "\n");

                            // Remove room from bookings
                            bookings.remove(room);
                            roomPrices.remove(room);
                        } else {
                            JOptionPane.showMessageDialog(this, "Payment failed. Please enter valid payment details!", "Error", JOptionPane.ERROR_MESSAGE);
                        }
                    } catch (NumberFormatException ex) {
                        JOptionPane.showMessageDialog(this, "Invalid number of days entered!", "Error", JOptionPane.ERROR_MESSAGE);
                    }
                }
            } else {
                JOptionPane.showMessageDialog(this, "Room not found or not booked!", "Error", JOptionPane.ERROR_MESSAGE);
            }
            billingRoomField.setText("");
        });
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            HotelManagementSystemWithPayment hms = new HotelManagementSystemWithPayment();
            hms.setVisible(true);
        });
    }
}