---
layout: default
title: Discord Integration
permalink: /discord-integration/
---

# Discord Integration Setup

Complete guide to setting up and configuring Discord bot integration with the BugBounty KSP platform.

## 🎯 Overview

The Discord integration allows your bug bounty platform to:
- Send real-time notifications to Discord channels
- Allow researchers to submit reports via Discord
- Query report status through bot commands
- Manage program announcements
- Foster community engagement

## 🚀 Prerequisites

Before you begin, ensure you have:
- A Discord account
- Administrator permissions in your Discord server
- Node.js 18+ installed
- BugBounty KSP platform running

## 📝 Creating a Discord Bot

### Step 1: Create Application

1. Go to [Discord Developer Portal](https://discord.com/developers/applications)
2. Click "New Application"
3. Enter application name: "BugBounty KSP Bot"
4. Click "Create"

### Step 2: Configure Bot

1. Navigate to the "Bot" tab
2. Click "Add Bot"
3. Confirm by clicking "Yes, do it!"
4. Under "Privileged Gateway Intents", enable:
   - ✅ Presence Intent
   - ✅ Server Members Intent
   - ✅ Message Content Intent
5. Click "Save Changes"

### Step 3: Get Bot Token

1. In the Bot tab, click "Reset Token"
2. Copy the token (you'll need this later)
3. **Important**: Never share this token publicly!

### Step 4: OAuth2 Configuration

1. Navigate to "OAuth2" > "URL Generator"
2. Select scopes:
   - ✅ `bot`
   - ✅ `applications.commands`
3. Select bot permissions:
   - ✅ Send Messages
   - ✅ Embed Links
   - ✅ Attach Files
   - ✅ Read Message History
   - ✅ Add Reactions
   - ✅ Use Slash Commands
4. Copy the generated URL

### Step 5: Invite Bot to Server

1. Paste the OAuth2 URL in your browser
2. Select your Discord server
3. Click "Authorize"
4. Complete the CAPTCHA

## ⚙️ Configuration

### Environment Variables

Add these to your `.env` file:

```env
# Discord Configuration
DISCORD_BOT_TOKEN=your_bot_token_here
DISCORD_CLIENT_ID=your_client_id_here
DISCORD_CLIENT_SECRET=your_client_secret_here
DISCORD_GUILD_ID=your_server_id_here

# Discord Channels
DISCORD_CHANNEL_ALERTS=channel_id_for_alerts
DISCORD_CHANNEL_REPORTS=channel_id_for_reports
DISCORD_CHANNEL_GENERAL=channel_id_for_general

# Discord Roles
DISCORD_ROLE_ADMIN=admin_role_id
DISCORD_ROLE_RESEARCHER=researcher_role_id
DISCORD_ROLE_CLIENT=client_role_id
```

### Getting Channel IDs

1. Enable Developer Mode in Discord:
   - User Settings > Advanced > Developer Mode
2. Right-click any channel
3. Click "Copy ID"

### Getting Role IDs

1. Right-click any role in Server Settings > Roles
2. Click "Copy ID"

## 🤖 Bot Implementation

### Basic Bot Setup

```typescript
import { Client, GatewayIntentBits, Events } from 'discord.js';

const client = new Client({
  intents: [
    GatewayIntentBits.Guilds,
    GatewayIntentBits.GuildMessages,
    GatewayIntentBits.MessageContent,
    GatewayIntentBits.GuildMembers,
  ],
});

client.once(Events.ClientReady, (c) => {
  console.log(`✅ Discord bot logged in as ${c.user.tag}`);
});

client.login(process.env.DISCORD_BOT_TOKEN);
```

### Slash Commands

#### Register Commands

```typescript
import { REST, Routes, SlashCommandBuilder } from 'discord.js';

const commands = [
  new SlashCommandBuilder()
    .setName('report')
    .setDescription('Submit a vulnerability report')
    .addStringOption(option =>
      option
        .setName('title')
        .setDescription('Report title')
        .setRequired(true)
    )
    .addStringOption(option =>
      option
        .setName('severity')
        .setDescription('Severity level')
        .setRequired(true)
        .addChoices(
          { name: 'Critical', value: 'critical' },
          { name: 'High', value: 'high' },
          { name: 'Medium', value: 'medium' },
          { name: 'Low', value: 'low' }
        )
    )
    .addStringOption(option =>
      option
        .setName('description')
        .setDescription('Detailed description')
        .setRequired(true)
    ),
  
  new SlashCommandBuilder()
    .setName('status')
    .setDescription('Check report status')
    .addStringOption(option =>
      option
        .setName('report_id')
        .setDescription('Report ID')
        .setRequired(true)
    ),
  
  new SlashCommandBuilder()
    .setName('programs')
    .setDescription('List active bug bounty programs'),
  
  new SlashCommandBuilder()
    .setName('stats')
    .setDescription('View your statistics'),
].map(command => command.toJSON());

// Register commands
const rest = new REST().setToken(process.env.DISCORD_BOT_TOKEN);

try {
  await rest.put(
    Routes.applicationGuildCommands(
      process.env.DISCORD_CLIENT_ID,
      process.env.DISCORD_GUILD_ID
    ),
    { body: commands }
  );
  console.log('✅ Slash commands registered');
} catch (error) {
  console.error('❌ Error registering commands:', error);
}
```

#### Handle Commands

```typescript
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isChatInputCommand()) return;

  switch (interaction.commandName) {
    case 'report':
      await handleReportCommand(interaction);
      break;
    case 'status':
      await handleStatusCommand(interaction);
      break;
    case 'programs':
      await handleProgramsCommand(interaction);
      break;
    case 'stats':
      await handleStatsCommand(interaction);
      break;
  }
});

async function handleReportCommand(interaction) {
  await interaction.deferReply({ ephemeral: true });
  
  const title = interaction.options.getString('title');
  const severity = interaction.options.getString('severity');
  const description = interaction.options.getString('description');
  
  try {
    // Submit report to API
    const response = await fetch(`${process.env.API_URL}/reports`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${getUserToken(interaction.user.id)}`
      },
      body: JSON.stringify({
        title,
        severity,
        description,
        source: 'discord'
      })
    });
    
    const report = await response.json();
    
    await interaction.editReply({
      content: `✅ Report submitted successfully!\nReport ID: ${report.id}`,
      ephemeral: true
    });
    
    // Send notification to alerts channel
    await sendReportNotification(report);
  } catch (error) {
    await interaction.editReply({
      content: '❌ Failed to submit report. Please try again.',
      ephemeral: true
    });
  }
}

async function handleStatusCommand(interaction) {
  await interaction.deferReply({ ephemeral: true });
  
  const reportId = interaction.options.getString('report_id');
  
  try {
    const response = await fetch(`${process.env.API_URL}/reports/${reportId}`, {
      headers: {
        'Authorization': `Bearer ${getUserToken(interaction.user.id)}`
      }
    });
    
    const report = await response.json();
    
    const embed = createReportEmbed(report);
    await interaction.editReply({ embeds: [embed], ephemeral: true });
  } catch (error) {
    await interaction.editReply({
      content: '❌ Report not found or access denied.',
      ephemeral: true
    });
  }
}
```

### Embeds

Rich message embeds for better presentation:

```typescript
import { EmbedBuilder } from 'discord.js';

function createReportEmbed(report) {
  const severityColors = {
    critical: 0xFF0000,
    high: 0xFF6600,
    medium: 0xFFCC00,
    low: 0x00FF00,
  };
  
  return new EmbedBuilder()
    .setTitle(`🐛 ${report.title}`)
    .setDescription(report.description.substring(0, 200))
    .setColor(severityColors[report.severity])
    .addFields(
      { name: '🎯 Program', value: report.program_name, inline: true },
      { name: '⚠️ Severity', value: report.severity.toUpperCase(), inline: true },
      { name: '📊 Status', value: report.status, inline: true },
      { name: '📅 Submitted', value: new Date(report.submitted_at).toLocaleDateString() },
    )
    .setFooter({ text: `Report ID: ${report.id}` })
    .setTimestamp();
}

function createProgramEmbed(program) {
  return new EmbedBuilder()
    .setTitle(program.name)
    .setDescription(program.description)
    .setColor(0x2196F3)
    .addFields(
      { 
        name: '💰 Rewards', 
        value: `$${program.reward_range.min} - $${program.reward_range.max}`,
        inline: true 
      },
      { 
        name: '📊 Reports', 
        value: `${program.statistics.total_reports}`,
        inline: true 
      },
      { 
        name: '✅ Status', 
        value: program.status,
        inline: true 
      },
    )
    .setTimestamp();
}
```

### Notifications

#### New Report Notification

```typescript
async function sendReportNotification(report) {
  const channel = await client.channels.fetch(process.env.DISCORD_CHANNEL_REPORTS);
  
  const embed = new EmbedBuilder()
    .setTitle('🚨 New Vulnerability Report')
    .setDescription(`**${report.title}**`)
    .setColor(report.severity === 'critical' ? 0xFF0000 : 0xFF6600)
    .addFields(
      { name: 'Severity', value: report.severity.toUpperCase(), inline: true },
      { name: 'Program', value: report.program_name, inline: true },
      { name: 'Researcher', value: report.researcher_name, inline: true },
    )
    .setTimestamp();
  
  await channel.send({ embeds: [embed] });
}
```

#### Status Update Notification

```typescript
async function sendStatusUpdateNotification(report, oldStatus, newStatus) {
  const channel = await client.channels.fetch(process.env.DISCORD_CHANNEL_ALERTS);
  
  const statusEmojis = {
    new: '🆕',
    triaged: '🔍',
    accepted: '✅',
    rejected: '❌',
    resolved: '✔️',
  };
  
  const embed = new EmbedBuilder()
    .setTitle(`${statusEmojis[newStatus]} Report Status Updated`)
    .setDescription(`Report **${report.title}** status changed`)
    .addFields(
      { name: 'Old Status', value: oldStatus, inline: true },
      { name: 'New Status', value: newStatus, inline: true },
      { name: 'Report ID', value: report.id },
    )
    .setColor(newStatus === 'accepted' ? 0x00FF00 : 0x2196F3)
    .setTimestamp();
  
  await channel.send({ embeds: [embed] });
  
  // DM the researcher
  const researcher = await client.users.fetch(report.researcher_discord_id);
  await researcher.send({ embeds: [embed] });
}
```

## 🔔 Webhooks

### Configure Webhooks

```typescript
async function setupWebhook(channelId) {
  const channel = await client.channels.fetch(channelId);
  
  const webhook = await channel.createWebhook({
    name: 'BugBounty KSP',
    avatar: 'https://your-logo-url.com/logo.png',
  });
  
  return webhook.url;
}

// Send messages via webhook
async function sendWebhookMessage(webhookUrl, content) {
  const webhook = new WebhookClient({ url: webhookUrl });
  
  await webhook.send({
    content: content.text,
    username: 'BugBounty Bot',
    avatarURL: 'https://your-logo-url.com/logo.png',
    embeds: content.embeds,
  });
}
```

## 🔐 Authentication

### Link Discord to Platform Account

```typescript
// OAuth2 flow
app.get('/auth/discord', (req, res) => {
  const discordAuthUrl = `https://discord.com/api/oauth2/authorize?client_id=${process.env.DISCORD_CLIENT_ID}&redirect_uri=${encodeURIComponent(process.env.DISCORD_REDIRECT_URI)}&response_type=code&scope=identify`;
  
  res.redirect(discordAuthUrl);
});

app.get('/auth/discord/callback', async (req, res) => {
  const { code } = req.query;
  
  try {
    // Exchange code for token
    const tokenResponse = await fetch('https://discord.com/api/oauth2/token', {
      method: 'POST',
      body: new URLSearchParams({
        client_id: process.env.DISCORD_CLIENT_ID,
        client_secret: process.env.DISCORD_CLIENT_SECRET,
        grant_type: 'authorization_code',
        code,
        redirect_uri: process.env.DISCORD_REDIRECT_URI,
      }),
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
    });
    
    const { access_token } = await tokenResponse.json();
    
    // Get user info
    const userResponse = await fetch('https://discord.com/api/users/@me', {
      headers: {
        Authorization: `Bearer ${access_token}`,
      },
    });
    
    const discordUser = await userResponse.json();
    
    // Link to platform account
    await linkDiscordAccount(req.user.id, discordUser.id);
    
    res.redirect('/settings?discord_linked=true');
  } catch (error) {
    res.redirect('/settings?discord_error=true');
  }
});
```

## 📊 Advanced Features

### Reaction Roles

Assign roles based on reactions:

```typescript
client.on(Events.MessageReactionAdd, async (reaction, user) => {
  if (user.bot) return;
  
  const roleMap = {
    '🔍': process.env.DISCORD_ROLE_RESEARCHER,
    '🏢': process.env.DISCORD_ROLE_CLIENT,
  };
  
  const roleId = roleMap[reaction.emoji.name];
  if (!roleId) return;
  
  const member = await reaction.message.guild.members.fetch(user.id);
  await member.roles.add(roleId);
});
```

### Leaderboard

Display top researchers:

```typescript
async function sendLeaderboard(channelId) {
  const response = await fetch(`${process.env.API_URL}/leaderboard`);
  const leaderboard = await response.json();
  
  const embed = new EmbedBuilder()
    .setTitle('🏆 Top Researchers')
    .setDescription('Monthly leaderboard')
    .setColor(0xFFD700);
  
  leaderboard.slice(0, 10).forEach((researcher, index) => {
    embed.addFields({
      name: `${index + 1}. ${researcher.name}`,
      value: `Reports: ${researcher.reports} | Bounties: $${researcher.total_earned}`,
    });
  });
  
  const channel = await client.channels.fetch(channelId);
  await channel.send({ embeds: [embed] });
}

// Schedule monthly
cron.schedule('0 0 1 * *', () => {
  sendLeaderboard(process.env.DISCORD_CHANNEL_GENERAL);
});
```

## 🐛 Troubleshooting

### Bot Not Responding

1. Check bot is online in Discord
2. Verify bot token is correct
3. Ensure intents are enabled
4. Check console for errors

### Commands Not Appearing

1. Wait up to 1 hour for global commands
2. Use guild commands for instant updates
3. Re-register commands
4. Check bot has `applications.commands` scope

### Permission Errors

1. Verify bot role is above roles it manages
2. Check channel permissions
3. Ensure required permissions are granted

## 📚 Resources

- [Discord.js Documentation](https://discord.js.org)
- [Discord Developer Portal](https://discord.com/developers)
- [Discord API Documentation](https://discord.com/developers/docs)

---

[← Knowledge Graph](knowledge-graph.md) | [Home](index.md) | [Next: Deployment →](deployment.md)
