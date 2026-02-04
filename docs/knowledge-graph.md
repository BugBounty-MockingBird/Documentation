# Knowledge Graph & Learning Paths Guide

Comprehensive guide to the Knowledge Graph system and Learning Paths in the BugBounty KSP platform.

## 🧠 What is a Knowledge Graph?

A Knowledge Graph is a structured representation of information that connects concepts, techniques, tools, and vulnerabilities in a meaningful way. In BugBounty KSP, the Knowledge Graph helps researchers:

- Discover related security concepts
- Understand attack techniques and defenses
- Navigate learning paths from beginner to expert
- Find relevant tools and resources
- Track their learning progress

## 🗺️ Knowledge Graph Structure

### Node Types

The Knowledge Graph consists of several node types:

```typescript
enum NodeType {
  CONCEPT = 'concept',           // Security concepts (e.g., "Authentication")
  TECHNIQUE = 'technique',       // Attack techniques (e.g., "SQL Injection")
  TOOL = 'tool',                 // Security tools (e.g., "Burp Suite")
  VULNERABILITY = 'vulnerability', // Specific vulnerabilities (e.g., "CVE-2024-1234")
  MITIGATION = 'mitigation',     // Defense techniques
  RESOURCE = 'resource',         // Learning resources (articles, videos)
  CERTIFICATION = 'certification' // Security certifications
}
```

### Relationship Types

Nodes are connected through various relationships:

```typescript
enum RelationshipType {
  REQUIRES = 'requires',           // Prerequisite knowledge
  RELATES_TO = 'relates_to',       // Related concepts
  PART_OF = 'part_of',            // Hierarchical relationship
  EXPLOITS = 'exploits',          // Technique exploits vulnerability
  MITIGATES = 'mitigates',        // Defense against technique
  USES = 'uses',                  // Technique uses tool
  LEADS_TO = 'leads_to',          // Learning progression
  DEMONSTRATES = 'demonstrates'    // Resource demonstrates concept
}
```

### Example Graph Structure

```
Concept: Web Security
  ├─ PART_OF ─> Concept: Application Security
  ├─ REQUIRES ─> Concept: HTTP Protocol
  └─ RELATES_TO ─> Concept: Network Security

Technique: SQL Injection
  ├─ PART_OF ─> Concept: Web Security
  ├─ EXPLOITS ─> Vulnerability: Unsanitized Input
  ├─ USES ─> Tool: sqlmap
  └─ MITIGATES ─> Mitigation: Parameterized Queries

Tool: Burp Suite
  ├─ USES ─> Technique: Parameter Fuzzing
  └─ DEMONSTRATES ─> Resource: Burp Suite Tutorial
```

## 📚 Creating Knowledge Nodes

### Adding a Concept

```typescript
interface ConceptNode {
  type: 'concept';
  title: string;
  description: string;
  difficulty: 'beginner' | 'intermediate' | 'advanced' | 'expert';
  tags: string[];
  content: {
    overview: string;
    keyPoints: string[];
    examples: string[];
    references: string[];
  };
}

// Example
const webSecurityConcept: ConceptNode = {
  type: 'concept',
  title: 'Web Application Security',
  description: 'Security principles and practices for web applications',
  difficulty: 'intermediate',
  tags: ['web', 'security', 'fundamentals'],
  content: {
    overview: 'Web application security involves protecting...',
    keyPoints: [
      'Understanding OWASP Top 10',
      'Secure authentication and authorization',
      'Input validation and output encoding',
      'Session management'
    ],
    examples: [
      'Implementing JWT authentication',
      'Preventing XSS attacks'
    ],
    references: [
      'https://owasp.org/www-project-top-ten/'
    ]
  }
};
```

### Adding a Technique

```typescript
interface TechniqueNode {
  type: 'technique';
  title: string;
  description: string;
  category: string;
  severity: 'critical' | 'high' | 'medium' | 'low';
  steps: string[];
  indicators: string[];
  mitigations: string[];
}

// Example
const sqlInjectionTechnique: TechniqueNode = {
  type: 'technique',
  title: 'SQL Injection',
  description: 'Exploiting SQL queries through unsanitized user input',
  category: 'Injection',
  severity: 'critical',
  steps: [
    '1. Identify input fields that interact with database',
    '2. Test for SQL injection vulnerability',
    '3. Craft malicious SQL payload',
    '4. Extract sensitive data or modify database'
  ],
  indicators: [
    'Error messages revealing database structure',
    'Unexpected query behavior',
    'Time-based delays in responses'
  ],
  mitigations: [
    'Use parameterized queries',
    'Implement input validation',
    'Apply principle of least privilege',
    'Use ORM frameworks'
  ]
};
```

### Adding a Tool

```typescript
interface ToolNode {
  type: 'tool';
  name: string;
  description: string;
  category: string;
  platform: string[];
  free: boolean;
  url: string;
  useCases: string[];
  alternatives: string[];
}

// Example
const burpSuite: ToolNode = {
  type: 'tool',
  name: 'Burp Suite',
  description: 'Integrated platform for web application security testing',
  category: 'Web Testing',
  platform: ['Windows', 'macOS', 'Linux'],
  free: false,
  url: 'https://portswigger.net/burp',
  useCases: [
    'Intercepting HTTP/HTTPS traffic',
    'Automated scanning for vulnerabilities',
    'Manual testing and fuzzing',
    'Extension development'
  ],
  alternatives: ['OWASP ZAP', 'Fiddler']
};
```

## 🎓 Learning Paths

Learning Paths are structured educational journeys through the Knowledge Graph.

### Path Structure

```typescript
interface LearningPath {
  id: string;
  title: string;
  description: string;
  difficulty: 'beginner' | 'intermediate' | 'advanced' | 'expert';
  estimatedHours: number;
  prerequisites: string[];
  stages: Stage[];
  certifications?: string[];
}

interface Stage {
  id: string;
  title: string;
  description: string;
  order: number;
  modules: Module[];
}

interface Module {
  id: string;
  title: string;
  nodeIds: string[];  // References to Knowledge Graph nodes
  exercises: Exercise[];
  assessments: Assessment[];
}
```

### Example Learning Path: Web Security Fundamentals

```typescript
const webSecurityPath: LearningPath = {
  id: 'web-security-fundamentals',
  title: 'Web Security Fundamentals',
  description: 'Master the basics of web application security',
  difficulty: 'beginner',
  estimatedHours: 40,
  prerequisites: [
    'Basic HTTP knowledge',
    'HTML/CSS fundamentals',
    'JavaScript basics'
  ],
  stages: [
    {
      id: 'stage-1',
      title: 'Foundation',
      description: 'Core concepts and principles',
      order: 1,
      modules: [
        {
          id: 'module-1-1',
          title: 'Introduction to Web Security',
          nodeIds: ['concept:web-security', 'concept:owasp-top-10'],
          exercises: [
            {
              title: 'Identify OWASP Top 10 in Sample App',
              type: 'practical',
              difficulty: 'easy'
            }
          ],
          assessments: [
            {
              title: 'Web Security Basics Quiz',
              questions: 10,
              passingScore: 80
            }
          ]
        },
        {
          id: 'module-1-2',
          title: 'HTTP Protocol Security',
          nodeIds: ['concept:http', 'concept:https', 'concept:tls'],
          exercises: [],
          assessments: []
        }
      ]
    },
    {
      id: 'stage-2',
      title: 'Common Vulnerabilities',
      description: 'Learn about common web vulnerabilities',
      order: 2,
      modules: [
        {
          id: 'module-2-1',
          title: 'Injection Attacks',
          nodeIds: [
            'technique:sql-injection',
            'technique:command-injection',
            'technique:xpath-injection'
          ],
          exercises: [
            {
              title: 'Find SQL Injection in Vulnerable App',
              type: 'lab',
              difficulty: 'medium'
            }
          ],
          assessments: []
        },
        {
          id: 'module-2-2',
          title: 'Cross-Site Scripting (XSS)',
          nodeIds: [
            'technique:reflected-xss',
            'technique:stored-xss',
            'technique:dom-xss'
          ],
          exercises: [
            {
              title: 'Exploit XSS Vulnerabilities',
              type: 'lab',
              difficulty: 'medium'
            }
          ],
          assessments: []
        }
      ]
    },
    {
      id: 'stage-3',
      title: 'Security Testing Tools',
      description: 'Master essential security testing tools',
      order: 3,
      modules: [
        {
          id: 'module-3-1',
          title: 'Burp Suite Basics',
          nodeIds: ['tool:burp-suite'],
          exercises: [
            {
              title: 'Configure Burp Suite Proxy',
              type: 'practical',
              difficulty: 'easy'
            },
            {
              title: 'Intercept and Modify Requests',
              type: 'practical',
              difficulty: 'medium'
            }
          ],
          assessments: []
        }
      ]
    }
  ],
  certifications: ['eWPT', 'OSWE']
};
```

## 🔍 Querying the Knowledge Graph

### Find Related Concepts

```graphql
query FindRelatedConcepts($nodeId: ID!) {
  node(id: $nodeId) {
    id
    title
    relationships(type: RELATES_TO) {
      target {
        id
        title
        type
      }
    }
  }
}
```

### Get Prerequisites

```graphql
query GetPrerequisites($nodeId: ID!) {
  node(id: $nodeId) {
    id
    title
    prerequisites: relationships(type: REQUIRES) {
      target {
        id
        title
        difficulty
      }
    }
  }
}
```

### Find Learning Path

```graphql
query FindLearningPath($from: ID!, $to: ID!) {
  shortestPath(from: $from, to: $to, relationshipType: LEADS_TO) {
    nodes {
      id
      title
      type
    }
    length
  }
}
```

## 📊 Tracking Progress

### User Progress Model

```typescript
interface UserProgress {
  userId: string;
  completedNodes: string[];
  inProgressNodes: string[];
  learningPaths: {
    pathId: string;
    status: 'not_started' | 'in_progress' | 'completed';
    progress: number; // 0-100
    currentStage: string;
    currentModule: string;
    startedAt: Date;
    completedAt?: Date;
  }[];
  achievements: Achievement[];
  skillLevel: {
    [category: string]: number; // 0-100
  };
}
```

### Marking Node as Complete

```typescript
async function completeNode(userId: string, nodeId: string) {
  await updateUserProgress(userId, {
    completedNodes: { $push: nodeId },
    inProgressNodes: { $pull: nodeId }
  });
  
  // Update skill level
  const node = await getNode(nodeId);
  await updateSkillLevel(userId, node.category, +5);
  
  // Check for achievements
  await checkAchievements(userId);
}
```

## 🏆 Achievements & Gamification

### Achievement Types

```typescript
interface Achievement {
  id: string;
  title: string;
  description: string;
  icon: string;
  category: string;
  criteria: {
    type: 'nodes_completed' | 'path_completed' | 'streak' | 'skill_level';
    target: number;
    category?: string;
  };
  reward: {
    points: number;
    badge: string;
  };
}

// Examples
const achievements: Achievement[] = [
  {
    id: 'first-steps',
    title: 'First Steps',
    description: 'Complete your first learning module',
    icon: '🎯',
    category: 'beginner',
    criteria: { type: 'nodes_completed', target: 1 },
    reward: { points: 10, badge: 'beginner' }
  },
  {
    id: 'web-security-master',
    title: 'Web Security Master',
    description: 'Complete the Web Security Fundamentals path',
    icon: '🔐',
    category: 'web',
    criteria: { type: 'path_completed', target: 1, category: 'web-security-fundamentals' },
    reward: { points: 100, badge: 'web-master' }
  },
  {
    id: 'consistent-learner',
    title: 'Consistent Learner',
    description: 'Maintain a 7-day learning streak',
    icon: '🔥',
    category: 'engagement',
    criteria: { type: 'streak', target: 7 },
    reward: { points: 50, badge: 'consistent' }
  }
];
```

## 🔧 Building the Knowledge Graph

### Data Import

```typescript
// Import from JSON
async function importKnowledgeGraph(data: KnowledgeGraphData) {
  const transaction = await db.startTransaction();
  
  try {
    // Import nodes
    for (const node of data.nodes) {
      await db.nodes.create(node);
    }
    
    // Import relationships
    for (const rel of data.relationships) {
      await db.relationships.create(rel);
    }
    
    await transaction.commit();
  } catch (error) {
    await transaction.rollback();
    throw error;
  }
}
```

### Validation

```typescript
function validateKnowledgeGraph(graph: KnowledgeGraph) {
  // Check for orphaned nodes
  const orphanedNodes = graph.nodes.filter(node => 
    !graph.relationships.some(rel => 
      rel.source === node.id || rel.target === node.id
    )
  );
  
  // Check for circular dependencies
  const hasCycles = detectCycles(graph);
  
  // Check for missing prerequisites
  const missingPrereqs = findMissingPrerequisites(graph);
  
  return {
    valid: orphanedNodes.length === 0 && !hasCycles && missingPrereqs.length === 0,
    orphanedNodes,
    hasCycles,
    missingPrereqs
  };
}
```

## 📈 Analytics

### Popular Learning Paths

```sql
SELECT 
  lp.id,
  lp.title,
  COUNT(up.user_id) as enrolled_users,
  AVG(up.progress) as avg_progress,
  AVG(DATEDIFF(up.completed_at, up.started_at)) as avg_completion_days
FROM learning_paths lp
JOIN user_progress up ON lp.id = up.path_id
WHERE up.status IN ('in_progress', 'completed')
GROUP BY lp.id
ORDER BY enrolled_users DESC
LIMIT 10;
```

### Skill Distribution

```sql
SELECT 
  skill_category,
  AVG(skill_level) as avg_level,
  COUNT(DISTINCT user_id) as user_count
FROM user_skills
GROUP BY skill_category
ORDER BY avg_level DESC;
```

## 🎨 Visualization

The Knowledge Graph can be visualized using:

- **Force-directed graph**: Show relationships between concepts
- **Tree view**: Display hierarchical structure
- **Path view**: Visualize learning progression
- **Heatmap**: Show popular nodes and paths

## 📚 Best Practices

1. **Granularity**: Keep nodes focused on single concepts
2. **Relationships**: Define clear, meaningful relationships
3. **Prerequisites**: Ensure prerequisites form a DAG (no cycles)
4. **Content Quality**: Provide comprehensive, accurate information
5. **Updates**: Regularly update with new techniques and tools
6. **Community**: Allow user contributions and reviews
7. **Accessibility**: Ensure content is accessible to different skill levels

---

[← n8n Workflows](n8n-workflows.md) | [Home](index.md) | [Next: Discord Integration →](discord-integration.md)
