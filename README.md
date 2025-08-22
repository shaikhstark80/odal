import java.util.*;

class Node {
    int x, y;
    int g, h, f;
    Node parent;

    public Node(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Node node = (Node) obj;
        return x == node.x && y == node.y;
    }

    @Override
    public int hashCode() {
        return Objects.hash(x, y);
    }
}

public class AStar {

    static int GRID_SIZE = 5;

    public static List<Node> findPath(int[][] grid, Node start, Node goal) {
        PriorityQueue<Node> openSet = new PriorityQueue<>(Comparator.comparingInt(node -> node.f));
        Set<Node> closedSet = new HashSet<>();

        start.g = 0;
        start.h = heuristic(start, goal);
        start.f = start.g + start.h;

        openSet.add(start);

        while (!openSet.isEmpty()) {
            Node current = openSet.poll();

            if (current.equals(goal)) {
                return reconstructPath(current);
            }

            closedSet.add(current);

            for (Node neighbor : getNeighbors(current, grid)) {
                if (closedSet.contains(neighbor)) {
                    continue;
                }

                int tentativeGScore = current.g + 1;

                if (!openSet.contains(neighbor) || tentativeGScore < neighbor.g) {
                    neighbor.g = tentativeGScore;
                    neighbor.h = heuristic(neighbor, goal);
                    neighbor.f = neighbor.g + neighbor.h;
                    neighbor.parent = current;

                    if (!openSet.contains(neighbor)) {
                        openSet.add(neighbor);
                    }
                }
            }
        }

        return null; // Path not found
    }

    private static List<Node> reconstructPath(Node current) {
        List<Node> path = new ArrayList<>();
        while (current != null) {
            path.add(current);
            current = current.parent;
        }
        Collections.reverse(path);
        return path;
    }

    private static int heuristic(Node node, Node goal) {
        // Manhattan distance
        return Math.abs(node.x - goal.x) + Math.abs(node.y - goal.y);
    }

    private static List<Node> getNeighbors(Node node, int[][] grid) {
        List<Node> neighbors = new ArrayList<>();
        int x = node.x;
        int y = node.y;

        // Possible movements: up, down, left, right
        int[][] directions = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

        for (int[] direction : directions) {
            int newX = x + direction[0];
            int newY = y + direction[1];

            if (isValid(newX, newY, grid)) {
                neighbors.add(new Node(newX, newY));
            }
        }

        return neighbors;
    }

    private static boolean isValid(int x, int y, int[][] grid) {
        return x >= 0 && x < GRID_SIZE && y >= 0 && y < GRID_SIZE && grid[x][y] != 1;
    }

    public static void main(String[] args) {
        int[][] grid = new int[GRID_SIZE][GRID_SIZE];

        // Mark obstacles
        grid[1][2] = 1;
        grid[2][2] = 1;
        grid[3][1] = 1;

        Node start = new Node(0, 0);
        Node goal = new Node(4, 4);

        List<Node> path = findPath(grid, start, goal);

        if (path != null) {
            System.out.println("Path found:");
            for (Node node : path) {
                System.out.print("(" + node.x + ", " + node.y + ") ");
            }
            System.out.println();
        } else {
            System.out.println("Path not found.");
        }
    }
}
