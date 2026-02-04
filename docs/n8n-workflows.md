---
layout: default
title: n8n Workflows
permalink: /n8n-workflows/
---

# n8n Workflow Guides

Comprehensive guide to automation workflows in the BugBounty KSP platform using n8n.

## 🔄 What is n8n?

n8n is an open-source workflow automation tool that enables you to connect different services and automate processes. In the BugBounty KSP platform, n8n is used for:

- Automating report notifications
- Processing submissions
- Integrating with external services
- Scheduling periodic tasks
- Data synchronization

## 🚀 Getting Started with n8n

### Installation

#### Using Docker

```bash
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

#### Using npm

```bash
npm install -g n8n
n8n start
```

### Access n8n UI

Once started, access the n8n interface at:
```
http://localhost:5678
```

### Configuration

Add these environment variables to connect n8n with BugBounty KSP:

```env
N8N_PORT=5678
N8N_PROTOCOL=http
N8N_HOST=localhost
WEBHOOK_URL=http://localhost:5678
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=secure_password

# BugBounty KSP API
BUGBOUNTY_API_URL=http://localhost:3000/api/v1
BUGBOUNTY_API_KEY=your_api_key
```

## 📊 Core Workflows

### 1. New Report Notification Workflow

Sends notifications when a new vulnerability report is submitted.

**Trigger**: Webhook  
**Actions**: Email, Discord, Slack notification

```json
{
  "name": "New Report Notification",
  "nodes": [
    {
      "type": "n8n-nodes-base.webhook",
      "name": "Webhook Trigger",
      "webhookId": "new-report",
      "path": "new-report"
    },
    {
      "type": "n8n-nodes-base.function",
      "name": "Process Report Data",
      "functionCode": "return [\n  {\n    json: {\n      title: $json.title,\n      severity: $json.severity,\n      program: $json.program_name,\n      researcher: $json.researcher_name\n    }\n  }\n];"
    },
    {
      "type": "n8n-nodes-base.discord",
      "name": "Discord Notification",
      "webhook_url": "{{$env.DISCORD_WEBHOOK_URL}}",
      "message": "🚨 New {{$json.severity}} severity report: {{$json.title}}"
    },
    {
      "type": "n8n-nodes-base.emailSend",
      "name": "Email Notification",
      "to": "security-team@example.com",
      "subject": "New Report: {{$json.title}}",
      "text": "A new {{$json.severity}} report has been submitted..."
    }
  ]
}
```

**Setup Steps**:
1. Create a new workflow in n8n
2. Add a Webhook trigger node
3. Configure webhook path: `/webhook/new-report`
4. Add Discord and Email nodes
5. Activate the workflow
6. Copy webhook URL to BugBounty KSP settings

### 2. Report Triage Automation

Automatically categorizes and assigns reports based on severity.

**Trigger**: Webhook  
**Logic**: Conditional routing based on severity

```json
{
  "name": "Report Triage",
  "nodes": [
    {
      "type": "n8n-nodes-base.webhook",
      "name": "New Report Webhook"
    },
    {
      "type": "n8n-nodes-base.switch",
      "name": "Route by Severity",
      "rules": [
        { "condition": "severity === 'critical'", "output": 0 },
        { "condition": "severity === 'high'", "output": 1 },
        { "condition": "true", "output": 2 }
      ]
    },
    {
      "type": "n8n-nodes-base.httpRequest",
      "name": "Assign to Senior Team",
      "method": "POST",
      "url": "{{$env.BUGBOUNTY_API_URL}}/reports/{{$json.id}}/assign",
      "body": { "team": "senior" }
    },
    {
      "type": "n8n-nodes-base.httpRequest",
      "name": "Assign to Regular Team",
      "method": "POST",
      "url": "{{$env.BUGBOUNTY_API_URL}}/reports/{{$json.id}}/assign",
      "body": { "team": "regular" }
    },
    {
      "type": "n8n-nodes-base.httpRequest",
      "name": "Auto-Triage Low Priority",
      "method": "PATCH",
      "url": "{{$env.BUGBOUNTY_API_URL}}/reports/{{$json.id}}",
      "body": { "status": "triaged", "priority": "low" }
    }
  ]
}
```

### 3. Periodic Statistics Report

Generates and sends weekly statistics reports.

**Trigger**: Cron (Weekly)  
**Actions**: Fetch data, generate report, send email

```json
{
  "name": "Weekly Statistics Report",
  "nodes": [
    {
      "type": "n8n-nodes-base.cron",
      "name": "Weekly Trigger",
      "cronExpression": "0 9 * * 1"
    },
    {
      "type": "n8n-nodes-base.httpRequest",
      "name": "Fetch Statistics",
      "method": "GET",
      "url": "{{$env.BUGBOUNTY_API_URL}}/analytics/weekly",
      "authentication": "headerAuth",
      "headerAuth": {
        "name": "Authorization",
        "value": "Bearer {{$env.BUGBOUNTY_API_KEY}}"
      }
    },
    {
      "type": "n8n-nodes-base.function",
      "name": "Format Report",
      "functionCode": "const stats = $json;\nreturn [{\n  json: {\n    subject: `Weekly Report - ${new Date().toLocaleDateString()}`,\n    body: `\n      Total Reports: ${stats.total_reports}\n      New Reports: ${stats.new_reports}\n      Resolved: ${stats.resolved}\n      Pending: ${stats.pending}\n      Total Paid: $${stats.total_paid}\n    `\n  }\n}];"
    },
    {
      "type": "n8n-nodes-base.emailSend",
      "name": "Send Report Email",
      "to": "management@example.com",
      "subject": "{{$json.subject}}",
      "text": "{{$json.body}}"
    }
  ]
}
```

### 4. Duplicate Report Detection

Checks for potential duplicate reports using similarity matching.

**Trigger**: Webhook  
**Actions**: Query existing reports, calculate similarity, flag duplicates

```json
{
  "name": "Duplicate Detection",
  "nodes": [
    {
      "type": "n8n-nodes-base.webhook",
      "name": "New Report Webhook"
    },
    {
      "type": "n8n-nodes-base.httpRequest",
      "name": "Fetch Similar Reports",
      "method": "GET",
      "url": "{{$env.BUGBOUNTY_API_URL}}/reports/search",
      "qs": {
        "program_id": "={{$json.program_id}}",
        "keywords": "={{$json.title}}"
      }
    },
    {
      "type": "n8n-nodes-base.function",
      "name": "Calculate Similarity",
      "functionCode": "// Implement similarity logic\nconst threshold = 0.8;\nconst isDuplicate = similarity > threshold;\nreturn [{ json: { isDuplicate, similarReports: results } }];"
    },
    {
      "type": "n8n-nodes-base.if",
      "name": "Check if Duplicate",
      "conditions": {
        "boolean": [{ "value1": "={{$json.isDuplicate}}", "value2": true }]
      }
    },
    {
      "type": "n8n-nodes-base.httpRequest",
      "name": "Flag as Potential Duplicate",
      "method": "PATCH",
      "url": "{{$env.BUGBOUNTY_API_URL}}/reports/{{$json.id}}",
      "body": {
        "flags": ["potential_duplicate"],
        "similar_reports": "={{$json.similarReports}}"
      }
    }
  ]
}
```

### 5. Researcher Engagement Workflow

Automatically follows up with researchers after report submission.

**Trigger**: Webhook  
**Timeline**: 
- Immediate: Confirmation email
- Day 1: Status update
- Day 7: Follow-up if no response

```json
{
  "name": "Researcher Engagement",
  "nodes": [
    {
      "type": "n8n-nodes-base.webhook",
      "name": "Report Submitted"
    },
    {
      "type": "n8n-nodes-base.emailSend",
      "name": "Immediate Confirmation",
      "to": "={{$json.researcher_email}}",
      "subject": "Report Received: {{$json.title}}",
      "text": "Thank you for your submission. We'll review it shortly."
    },
    {
      "type": "n8n-nodes-base.wait",
      "name": "Wait 24 Hours",
      "amount": 24,
      "unit": "hours"
    },
    {
      "type": "n8n-nodes-base.httpRequest",
      "name": "Check Report Status",
      "method": "GET",
      "url": "{{$env.BUGBOUNTY_API_URL}}/reports/{{$json.id}}"
    },
    {
      "type": "n8n-nodes-base.if",
      "name": "Status Changed?",
      "conditions": {
        "string": [{ 
          "value1": "={{$json.status}}", 
          "operation": "notEqual",
          "value2": "new" 
        }]
      }
    },
    {
      "type": "n8n-nodes-base.emailSend",
      "name": "Status Update Email",
      "to": "={{$json.researcher_email}}",
      "subject": "Update on Your Report",
      "text": "Your report status: {{$json.status}}"
    }
  ]
}
```

## 🔗 Integration Points

### Webhook Configuration in BugBounty KSP

Configure webhooks in your application settings:

```typescript
// Example webhook configuration
const webhookConfig = {
  new_report: {
    url: 'http://localhost:5678/webhook/new-report',
    events: ['report.created'],
    active: true
  },
  report_updated: {
    url: 'http://localhost:5678/webhook/report-updated',
    events: ['report.status_changed'],
    active: true
  }
};
```

### API Authentication

Use API keys for n8n to authenticate with BugBounty KSP:

```javascript
// In n8n HTTP Request nodes
{
  "authentication": "headerAuth",
  "headerAuth": {
    "name": "Authorization",
    "value": "Bearer {{$env.BUGBOUNTY_API_KEY}}"
  }
}
```

## 🛠️ Advanced Patterns

### Error Handling

```json
{
  "type": "n8n-nodes-base.errorTrigger",
  "name": "On Error",
  "actions": [
    {
      "type": "n8n-nodes-base.emailSend",
      "name": "Error Notification",
      "to": "admin@example.com",
      "subject": "Workflow Error",
      "text": "Error in workflow: {{$json.error}}"
    }
  ]
}
```

### Retry Logic

```json
{
  "type": "n8n-nodes-base.httpRequest",
  "name": "API Call with Retry",
  "options": {
    "retry": {
      "enabled": true,
      "maxTries": 3,
      "waitBetween": 1000
    }
  }
}
```

### Data Transformation

```javascript
// Function node for complex transformations
const items = $input.all();
return items.map(item => ({
  json: {
    id: item.json.id,
    formattedDate: new Date(item.json.created_at).toLocaleDateString(),
    severityLevel: item.json.severity.toUpperCase(),
    isUrgent: ['critical', 'high'].includes(item.json.severity)
  }
}));
```

## 📝 Best Practices

1. **Use Environment Variables**: Store sensitive data in environment variables
2. **Error Handling**: Always add error trigger nodes
3. **Testing**: Test workflows with sample data before activation
4. **Monitoring**: Enable workflow execution logs
5. **Documentation**: Document complex workflows with notes
6. **Modular Design**: Break complex workflows into sub-workflows
7. **Rate Limiting**: Implement delays between API calls

## 🔍 Debugging Workflows

### Enable Debug Mode

```bash
export N8N_LOG_LEVEL=debug
n8n start
```

### View Execution Data

In n8n UI:
1. Go to Executions tab
2. Click on failed execution
3. View input/output data for each node
4. Check error messages

### Common Issues

**Webhook not triggering**:
- Verify webhook URL is correct
- Check webhook is activated
- Ensure firewall allows traffic

**API authentication failing**:
- Verify API key is valid
- Check Authorization header format
- Confirm API endpoint URL

**Timeout errors**:
- Increase timeout in HTTP Request node
- Add retry logic
- Check API server status

## 📚 Resources

- [n8n Documentation](https://docs.n8n.io)
- [BugBounty KSP API Reference](api-reference.md)
- [Workflow Templates](#)
- [Community Forums](#)

---

[← Frontend Components](frontend-components.md) | [Home](index.md) | [Next: Knowledge Graph →](knowledge-graph.md)
