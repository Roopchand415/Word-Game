# Word-Game
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.HashMap;
import java.util.Map;

public class WordGame extends JFrame {
    private JTextField numberOfLettersField;
    private JTextArea lettersAndScoresArea;
    private JTextField wordField;
    private JButton submitLettersButton;
    private JButton submitWordButton;
    private JButton newGameButton;
    private JLabel scoreLabel;
    private JLabel currentPlayerLabel;
    private JPanel panel;
    private Map<Character, Integer> letterScores;
    private int numberOfLetters;
    private int player1Score = 0;
    private int player2Score = 0;
    private boolean isPlayer1Turn = true;

    public WordGame() {
        setTitle("Word Game");
        setSize(800, 600);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        panel = new JPanel(new GridBagLayout());
        panel.setBackground(new Color(245, 245, 245));
        getContentPane().add(panel);

        initializeUIComponents();

        setVisible(true);
    }

    private void initializeUIComponents() {
        GridBagConstraints gbc = new GridBagConstraints();
        gbc.insets = new Insets(10, 10, 10, 10);

        JLabel titleLabel = new JLabel("Word Game");
        titleLabel.setFont(new Font("Serif", Font.BOLD, 36));
        titleLabel.setHorizontalAlignment(SwingConstants.CENTER);
        titleLabel.setForeground(new Color(25, 25, 112));
        gbc.gridx = 0;
        gbc.gridy = 0;
        gbc.gridwidth = 2;
        gbc.fill = GridBagConstraints.HORIZONTAL;
        panel.add(titleLabel, gbc);

        JLabel numberOfLettersLabel = new JLabel("Enter number of letters (3-15):");
        numberOfLettersLabel.setFont(new Font("SansSerif", Font.PLAIN, 18));
        gbc.gridx = 0;
        gbc.gridy = 1;
        gbc.gridwidth = 1;
        gbc.anchor = GridBagConstraints.EAST;
        panel.add(numberOfLettersLabel, gbc);

        numberOfLettersField = new JTextField(10);
        numberOfLettersField.setFont(new Font("SansSerif", Font.PLAIN, 18));
        gbc.gridx = 1;
        gbc.gridy = 1;
        gbc.anchor = GridBagConstraints.WEST;
        panel.add(numberOfLettersField, gbc);

        submitLettersButton = new JButton("Submit Letters");
        submitLettersButton.setFont(new Font("SansSerif", Font.PLAIN, 18));
        submitLettersButton.setBackground(new Color(135, 206, 250));
        gbc.gridx = 0;
        gbc.gridy = 2;
        gbc.gridwidth = 2;
        gbc.anchor = GridBagConstraints.CENTER;
        panel.add(submitLettersButton, gbc);

        lettersAndScoresArea = new JTextArea(10, 30);
        lettersAndScoresArea.setFont(new Font("Monospaced", Font.PLAIN, 16));
        lettersAndScoresArea.setEditable(false);
        lettersAndScoresArea.setBorder(BorderFactory.createLineBorder(Color.GRAY));
        JScrollPane scrollPane = new JScrollPane(lettersAndScoresArea);
        gbc.gridx = 0;
        gbc.gridy = 3;
        gbc.gridwidth = 2;
        gbc.fill = GridBagConstraints.BOTH;
        gbc.weightx = 1.0;
        gbc.weighty = 1.0;
        panel.add(scrollPane, gbc);

        JLabel wordLabel = new JLabel("Enter word:");
        wordLabel.setFont(new Font("SansSerif", Font.PLAIN, 18));
        gbc.gridx = 0;
        gbc.gridy = 4;
        gbc.gridwidth = 1;
        gbc.fill = GridBagConstraints.HORIZONTAL;
        gbc.anchor = GridBagConstraints.EAST;
        panel.add(wordLabel, gbc);

        wordField = new JTextField(10);
        wordField.setFont(new Font("SansSerif", Font.PLAIN, 18));
        gbc.gridx = 1;
        gbc.gridy = 4;
        gbc.anchor = GridBagConstraints.WEST;
        panel.add(wordField, gbc);

        submitWordButton = new JButton("Submit Word");
        submitWordButton.setFont(new Font("SansSerif", Font.PLAIN, 18));
        submitWordButton.setBackground(new Color(144, 238, 144));
        gbc.gridx = 0;
        gbc.gridy = 5;
        gbc.gridwidth = 2;
        gbc.anchor = GridBagConstraints.CENTER;
        panel.add(submitWordButton, gbc);

        scoreLabel = new JLabel("Player 1: 0 | Player 2: 0");
        scoreLabel.setFont(new Font("SansSerif", Font.BOLD, 20));
        scoreLabel.setForeground(new Color(25, 25, 112));
        gbc.gridx = 0;
        gbc.gridy = 6;
        gbc.gridwidth = 2;
        gbc.anchor = GridBagConstraints.CENTER;
        panel.add(scoreLabel, gbc);

        currentPlayerLabel = new JLabel("Current Turn: Player 1");
        currentPlayerLabel.setFont(new Font("SansSerif", Font.BOLD, 20));
        currentPlayerLabel.setForeground(new Color(25, 25, 112));
        gbc.gridx = 0;
        gbc.gridy = 7;
        gbc.gridwidth = 2;
        gbc.anchor = GridBagConstraints.CENTER;
        panel.add(currentPlayerLabel, gbc);

        newGameButton = new JButton("New Game");
        newGameButton.setFont(new Font("SansSerif", Font.PLAIN, 18));
        newGameButton.setBackground(new Color(255, 182, 193));
        gbc.gridx = 0;
        gbc.gridy = 8;
        gbc.gridwidth = 2;
        gbc.anchor = GridBagConstraints.CENTER;
        panel.add(newGameButton, gbc);

        submitLettersButton.addActionListener(new SubmitLettersButtonListener());
        submitWordButton.addActionListener(new SubmitWordButtonListener());
        newGameButton.addActionListener(new NewGameButtonListener());

        letterScores = new HashMap<>();
    }

    private class SubmitLettersButtonListener implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            try {
                String input = numberOfLettersField.getText();
                numberOfLetters = Integer.parseInt(input);

                if (numberOfLetters < 3 || numberOfLetters > 15) {
                    JOptionPane.showMessageDialog(panel, "Number of letters must be between 3 and 15.");
                    return;
                }

                lettersAndScoresArea.setText(""); // Clear previous letters and scores
                letterScores.clear(); // Clear previous scores

                for (int i = 0; i < numberOfLetters; i++) {
                    String letterInput = JOptionPane.showInputDialog(panel, "Enter letter and score (e.g., A 3):");
                    if (letterInput == null || letterInput.trim().isEmpty()) {
                        JOptionPane.showMessageDialog(panel, "Invalid input. Please enter a letter and score.");
                        i--;
                        continue;
                    }
                    String[] parts = letterInput.trim().split("\\s+");
                    if (parts.length != 2) {
                        JOptionPane.showMessageDialog(panel, "Invalid input. Please enter a letter and score.");
                        i--;
                        continue;
                    }
                    char letter = parts[0].toUpperCase().charAt(0);
                    if (!Character.isLetter(letter)) {
                        JOptionPane.showMessageDialog(panel, "Invalid letter. Please enter a valid letter.");
                        i--;
                        continue;
                    }
                    int score = Integer.parseInt(parts[1]);
                    if (score <= 0 || score >= 10) {
                        JOptionPane.showMessageDialog(panel, "Score must be between 1 and 9.");
                        i--;
                        continue;
                    }
                    letterScores.put(letter, score);
                    lettersAndScoresArea.append(letter + " - " + score + "\n");
                }
            } catch (NumberFormatException ex) {
                JOptionPane.showMessageDialog(panel, "Please enter a valid number.");
            }
        }
    }

    private class SubmitWordButtonListener implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            String word = wordField.getText().toUpperCase();
            int totalScore = 0;
            for (char c : word.toCharArray()) {
                totalScore += letterScores.getOrDefault(c, 0);
            }
            if (isPlayer1Turn) {
                player1Score += totalScore;
            } else {
                player2Score += totalScore;
            }
            scoreLabel.setText("Player 1: " + player1Score + " | Player 2: " + player2Score);
            isPlayer1Turn = !isPlayer1Turn;
            currentPlayerLabel.setText("Current Turn: " + (isPlayer1Turn ? "Player 1" : "Player 2"));
            wordField.setText("");
        }
    }

    private class NewGameButtonListener implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            numberOfLettersField.setText("");
            lettersAndScoresArea.setText("");
            wordField.setText("");
            player1Score = 0;
            player2Score = 0;
            scoreLabel.setText("Player 1: 0 | Player 2: 0");
            currentPlayerLabel.setText("Current Turn: Player 1");
            isPlayer1Turn = true;
            letterScores.clear();
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(WordGame::new);
    }
}
