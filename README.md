# java
User.java
package tracker;

public class User {
    private int id;
    private String name;
    private String role;  
    private String email;

    public User(int id, String name, String role, String email) {
        this.id = id;
        this.name = name;
        this.role = role;
        this.email = email;
    }

    
    public int getId() { return id; }
    public String getName() { return name; }
    public String getRole() { return role; }
    public String getEmail() { return email; }

    
    public void setEmail(String email) { this.email = email; }

    public void display() {
        System.out.println("User: " + id + " | " + name + " | " + role);
    }
}


Manager.java
package tracker;

public class Manager extends User {
    public Manager(int id, String name, String email) {
        super(id, name, "Manager", email);
    }

    @Override
    public void display() {
        System.out.println("Manager: " + getName() + " approves issues.");
    }

    public void approveIssue(Issue issue) {
        System.out.println("Manager approved issue: " + issue.getTitle());
    }
}

Issue.java
package tracker;

public class Issue {
    private int issueId;
    private String title;
    private String description;
    private String severity; 
    private String status;   
    private User assignee;

    public Issue(int issueId, String title, String description, String severity) {
        this.issueId = issueId;
        this.title = title;
        this.description = description;
        this.severity = severity;
        this.status = "NEW";
    }

    
    public int getIssueId() { return issueId; }
    public String getTitle() { return title; }
    public String getSeverity() { return severity; }
    public String getStatus() { return status; }
    public User getAssignee() { return assignee; }

    
    public void assignTo(User user) { this.assignee = user; }
    public void changeStatus(String newStatus) { this.status = newStatus; }

    public void display() {
        System.out.println("Issue #" + issueId + " [" + title + "] Severity: " + severity +
                " | Status: " + status +
                " | Assigned to: " + (assignee != null ? assignee.getName() : "Unassigned"));
    }
}

Bug.java
package tracker;

public class Bug extends Issue {
    public Bug(int id, String title, String desc, String severity) {
        super(id, title, desc, severity);
    }

    @Override
    public void display() {
        System.out.println("🐞 Bug: " + getTitle() + " | Severity: " + getSeverity() +
                " | Status: " + getStatus());
    }
}

Task.java
package tracker;

public class Task extends Issue {
    public Task(int id, String title, String desc, String severity) {
        super(id, title, desc, severity);
    }

    @Override
    public void display() {
        System.out.println("📝 Task: " + getTitle() + " | Status: " + getStatus());
    }
}

Project.java
package tracker;

import java.util.*;

public class Project {
    private int projectId;
    private String name;
    private String repoUrl;
    private List<Issue> backlog;
    private List<User> team;

    public Project(int projectId, String name, String repoUrl) {
        this.projectId = projectId;
        this.name = name;
        this.repoUrl = repoUrl;
        this.backlog = new ArrayList<>();
        this.team = new ArrayList<>();
    }

    public void addUser(User u) { team.add(u); }
    public void addIssue(Issue i) { backlog.add(i); }

    public void displayDashboard() {
        System.out.println("\n--- Project Dashboard: " + name + " ---");
        for (Issue issue : backlog) {
            issue.display(); 
        }
    }

    public void listBySeverity(String severity) {
        System.out.println("\nIssues with severity " + severity + ":");
        for (Issue issue : backlog) {
            if (issue.getSeverity().equalsIgnoreCase(severity)) {
                issue.display();
            }
        }
    }
}

TrackerService.java
package tracker;

public class TrackerService {
    private int issueCounter = 1;

   
    public Issue createIssue(Project project, String title, String desc, String severity) {
        Issue issue = new Bug(issueCounter++, title, desc, severity);
        project.addIssue(issue);
        return issue;
    }

    public Issue createIssue(Project project, String title, String desc, String severity, String tag) {
        Issue issue = new Task(issueCounter++, title + " [Tag:" + tag + "]", desc, severity);
        project.addIssue(issue);
        return issue;
    }

    public void assignIssue(Issue issue, User user) {
        issue.assignTo(user);
    }

    public void updateStatus(Issue issue, String newStatus) {
        issue.changeStatus(newStatus);
    }
}


TrackerAppMain.java
package tracker;

public class TrackerAppMain {
    public static void main(String[] args) {
        TrackerService service = new TrackerService();

              Project p1 = new Project(101, "Payment System", "github.com/project/payment");

        
        User dev = new User(1, "Alice", "Dev", "alice@dev.com");
        User qa = new User(2, "Bob", "QA", "bob@qa.com");
        Manager mgr = new Manager(3, "Charlie", "charlie@mgr.com");

        p1.addUser(dev);
        p1.addUser(qa);
        p1.addUser(mgr);

        
        Issue i1 = service.createIssue(p1, "Login fails", "Error on invalid credentials", "HIGH");
        Issue i2 = service.createIssue(p1, "UI Enhancement", "Improve dashboard layout", "LOW", "UI");

       
        service.assignIssue(i1, dev);
        service.assignIssue(i2, qa);

        
        service.updateStatus(i1, "IN_PROGRESS");
        service.updateStatus(i1, "RESOLVED");
        service.updateStatus(i1, "CLOSED");
        mgr.approveIssue(i1);

        // Show dashboards & reports
        p1.displayDashboard();
        p1.listBySeverity("HIGH");
    }
}

