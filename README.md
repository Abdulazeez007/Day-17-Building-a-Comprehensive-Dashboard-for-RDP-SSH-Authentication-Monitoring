# Day-17-Building-a-Comprehensive-Dashboard-for-RDP-SSH-Authentication-Monitoring

In today’s cybersecurity landscape, monitoring both RDP (Remote Desktop Protocol) and SSH (Secure Shell) activity is critical for safeguarding systems against unauthorized access. Following our previous post on SSH activity tracking, this blog will focus on creating a robust dashboard for RDP authentication attempts while also enhancing our existing SSH dashboard.

By the end of this guide, you’ll have a single dashboard that visually represents failed and successful authentication attempts for both RDP and SSH, helping you identify patterns, track access points, and stay one step ahead of potential security threats.

## Step 1: Setting Up RDP Failed Authentication Monitoring

To get started, log into your Elastic web GUI and navigate to the Maps section by clicking on the hamburger menu in the top left corner. This is where we will build our visualizations.

### Retrieving the Query

Since I couldn’t recall the exact query from memory, I went back to the Discover tab to check the saved query we used previously. I set the time range to 7 days to ensure I had sufficient data, and then found the relevant query:

```bash
event.code: 4625
```
This query is looking for event code 4625, which signals a failed authentication attempt in Windows logs. However, we want to narrow it down to a specific machine for better focus. So, I modified the query to:

```bash
event.code: 4625 and agent.name: Aurora
```

With the query saved, we’re ready to head back to the Maps tab and start building our visual layer.

### Building the Map Layer

Now that we have the query, let’s create a layer in the map visualization. Click on **Add Layer** and choose **Choropleth** as the type. For EMS Boundaries, select **World Countries**, and for the field, pick **source.geo.country_iso_code**. This will allow us to visually plot the failed RDP authentication attempts by country.

Once you’ve added the layer, be sure to save it and name your dashboard **RDP Failed Authentication**. Now you have a real-time view of where these failed attempts are originating from across the globe.

## Step 2: Tracking Successful RDP Logins

Now that we’ve got failed attempts covered, it’s time to track successful RDP logins. For this, we’ll be focusing on event code **4624**, which corresponds to successful authentication attempts in Windows.

### Understanding Logon Types

When dealing with RDP logins, there are two logon types to track:

- **Logon Type 10**: Indicates a Remote Interactive logon, which is specific to RDP.
- **Logon Type 7**: Refers to an unlock event, which can also be triggered through an RDP session.

To filter successful RDP logins, I used this query:

```bash
event.code: 4624 and (winlog.event_data.LogonType: 10 or winlog.event_data.LogonType: 7)
```

### Adding the Successful Authentication Layer

Just like we did with the failed attempts, you’ll want to duplicate the **RDP Failed Authentication** map and update the query to the one above. Rename the new layer **RDP Successful Authentications** and save it. Now your dashboard is equipped to track both failed and successful RDP attempts.

## Step 3: Enhancing the Dashboard with Detailed Tables

A map gives a great visual overview, but for more granular insights, tables are extremely useful. Let’s add a table that lists the username, source IP, and the country of origin for each failed and successful authentication.

### Creating Tables for SSH Activity

Since we’ve already built an SSH failed authentication dashboard in a previous post, let’s incorporate that data into this new dashboard as well.

Head back to Discover and open the saved SSH query for failed logins:

```bash
system.auth.ssh.event: * and agent.name: Aurora and system.auth.ssh.event: Failed
```

Create a new table visualization, and add the fields **source.ip**, **user.name**, and **source.geo.country_name**.

Save the table as **SSH Failed Authentication [Table]**.

To mirror this, duplicate the table, change the query to track successful SSH logins, and save it as **SSH Successful Authentication [Table]**.

### Creating Tables for RDP Activity

Now, let’s apply the same logic to the RDP activity. Duplicate the **SSH Failed Authentication [Table]**, and modify the query to:

```bash
event.code: 4625 and agent.name: Aurora
```

This table will display all failed RDP authentication attempts. Then, duplicate the table again, this time updating the query to:

```bash
event.code: 4624 and agent.name: Aurora
```

This will capture all successful RDP attempts. Be sure to rename each table accordingly.

## Step 4: Final Touches and Insights

Your dashboard is now equipped with both maps and tables, offering a holistic view of all authentication attempts — both successful and failed — via RDP and SSH.

At a glance, you can see:

- Which usernames were used for authentication attempts.
- The source IP addresses of those attempts.
- The countries from which these attempts originated.
- The total number of attempts and their outcomes (successful or failed).

  ![Alt text](https://raw.githubusercontent.com/Virus192/Day-17-Building-a-Comprehensive-Dashboard-for-RDP-SSH-Authentication-Monitoring/refs/heads/main/Images/photo_6003690240981714181_w.jpg)
  **SSH Activity**

  ![Alt text](https://raw.githubusercontent.com/Virus192/Day-17-Building-a-Comprehensive-Dashboard-for-RDP-SSH-Authentication-Monitoring/refs/heads/main/Images/photo_6003690240981714182_w.jpg)
  **RDP Activity**


## Step 5: Save Your Work

Before wrapping up, double-check that everything is saved correctly. This ensures you don’t lose any progress as you fine-tune your dashboard. Now you should have two primary dashboards — one for failed authentication attempts and another for successful ones — covering both RDP and SSH activity.

## What’s Next?

In the next post, we’ll dive into one of the common attack tactics known as Command and Control (C2). We’ll explore how adversaries use frameworks like Mythic to establish C2 sessions and how you can detect and mitigate these activities. Stay tuned!

## Conclusion

Creating this dashboard not only provides a real-time overview of RDP and SSH activity but also equips you with the tools to monitor, investigate, and respond to any suspicious authentication attempts. With visual maps and detailed tables, your security operations can now pinpoint threats quickly and efficiently, helping to maintain a secure environment.

Feel free to follow along with these steps to build your own monitoring dashboard in Elastic, and stay on the lookout for the next blog on how to detect C2 sessions!


