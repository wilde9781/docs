# ☕ Yaml complete course

# ☕ YAML: A Developer's Guide (Chai Edition)

*"Like brewing the perfect cup of chai, writing YAML is all about balance, structure, and getting the proportions just right."*

## 📋 Table of Contents

1. [What is YAML?](#what-is-yaml)
2. [Basic Syntax](#basic-syntax)
3. [Data Types](#data-types)
4. [Collections](#collections)
5. [Advanced Features](#advanced-features)
6. [Common Pitfalls](#common-pitfalls)
7. [Real-World Examples](#real-world-examples)
8. [Best Practices](#best-practices)

---

## What is YAML?

**YAML** stands for "YAML Ain't Markup Language" (recursive acronym)

- Human-readable data serialization format
- Commonly used for configuration files
- Superset of JSON
- File extensions: `.yaml` or `.yml`

**Why YAML?**

- Clean, readable syntax
- Supports comments
- Less verbose than JSON or XML
- Native support for complex data structures

---

## Basic Syntax

### Key-Value Pairs

Like the basic ingredients of chai: tea and water.

```yaml
# Simple key-value pairs
chai_type: masala_chai
temperature: hot
servings: 2
brewing_time: 5
```

### Comments

```yaml
# This is a single-line comment
chai_type: masala_chai  # Inline comment

# Multi-line thoughts
# can be expressed
# like this
```

### Indentation Rules

**CRITICAL:** YAML uses **spaces** for indentation, NOT tabs!

```yaml
# Good - 2 spaces (recommended)
chai_recipe:
  base: black_tea
  milk: whole_milk

# Also valid - 4 spaces
chai_recipe:
    base: black_tea
    milk: whole_milk

# Bad - mixing spaces and tabs (will cause errors!)
# Don't do this!
```

---

## Data Types

### Strings

```yaml
# Unquoted strings
chai_name: Masala Chai

# Quoted strings (when special characters are present)
description: "Chai with cardamom, ginger & cloves"
tagline: 'The best chai you''ll ever taste!'

# Multi-line strings - Literal style (preserves newlines)
brewing_instructions: |
  Boil water in a pot
  Add tea leaves and spices
  Simmer for 3-5 minutes
  Add milk and sugar
  Strain and serve hot

# Multi-line strings - Folded style (converts newlines to spaces)
chai_story: >
  Chai has been a staple beverage in India for centuries.
  It brings people together and starts conversations.
  Every household has their own special recipe.
```

### Numbers

```yaml
# Integers
cups_per_day: 3
servings: 2

# Floats
tea_leaves_grams: 5.5
milk_ratio: 0.75

# Scientific notation
caffeine_mg: 1.2e+2  # 120 mg

# Octal (prefix with 0o)
permission: 0o755

# Hexadecimal (prefix with 0x)
color_code: 0xFF5733
```

### Booleans

```yaml
# Various ways to express true
is_hot: true
add_sugar: yes
organic: on
available: True

# Various ways to express false
is_cold: false
add_salt: no
instant: off
expired: False
```

### Null Values

```yaml
# Different ways to represent null
sweetener: null
alternative_milk: ~
optional_spice:
```

### Dates and Timestamps

```yaml
# ISO 8601 format
morning_brew: 2025-01-15
exact_time: 2025-01-15T08:30:00Z
local_time: 2025-01-15 08:30:00
```

---

## Collections

### Lists (Arrays)

```yaml
# Block style (recommended)
spices:
  - cardamom
  - ginger
  - cinnamon
  - cloves
  - black_pepper

# Flow style (inline)
spices: [cardamom, ginger, cinnamon, cloves, black_pepper]

# List of numbers
steeping_times: [3, 5, 7, 10]

# Mixed types
chai_facts:
  - "Origin: India"
  - 2000  # Years of history
  - true  # Is delicious
```

### Nested Lists

```yaml
chai_categories:
  - name: Traditional
    varieties:
      - Masala Chai
      - Ginger Chai
      - Cardamom Chai
  - name: Modern
    varieties:
      - Vanilla Chai
      - Chocolate Chai
      - Pumpkin Spice Chai
```

### Dictionaries (Objects/Maps)

```yaml
# Nested dictionaries
masala_chai:
  ingredients:
    tea: black_tea
    liquid:
      water: 200ml
      milk: 100ml
    spices:
      cardamom: 2_pods
      ginger: 1_inch
      cinnamon: 1_stick
  preparation:
    method: simmer
    duration: 5_minutes
    temperature: medium_heat
```

### List of Dictionaries

```yaml
chai_menu:
  - name: Masala Chai
    price: 30
    size: regular
    spicy: true
  - name: Ginger Chai
    price: 35
    size: large
    spicy: true
  - name: Plain Chai
    price: 25
    size: regular
    spicy: false
```

### Dictionary of Lists

```yaml
chai_shop_inventory:
  teas:
    - Assam Black Tea
    - Darjeeling Tea
    - Ceylon Tea
  spices:
    - Cardamom
    - Ginger
    - Cinnamon
  additives:
    - Honey
    - Sugar
    - Jaggery
```

---

## Advanced Features

### Anchors and Aliases (DRY - Don't Repeat Yourself)

Like making a chai concentrate and using it multiple times!

```yaml
# Define an anchor with &
default_chai_base: &default_base
  tea: black_tea
  water: 200ml
  brewing_time: 5

# Reference it with *
morning_chai:
  <<: *default_base
  milk: 100ml
  sugar: 2_tsp

evening_chai:
  <<: *default_base
  milk: 150ml
  sugar: 1_tsp
  spices:
    - cardamom
    - ginger

# The above expands to:
# morning_chai:
#   tea: black_tea
#   water: 200ml
#   brewing_time: 5
#   milk: 100ml
#   sugar: 2_tsp
```

### Merge Keys

```yaml
# Define reusable components
standard_spices: &spices
  cardamom: 2
  ginger: 1_inch
  cinnamon: 1_stick

premium_spices: &premium
  <<: *spices
  saffron: 3_strands
  star_anise: 1

# Use in recipes
regular_chai:
  <<: *spices
  tea: black_tea

special_chai:
  <<: *premium
  tea: premium_blend
```

### Multi-line Keys

```yaml
? |
  This is a very long key
  that spans multiple lines
: "And this is its value"
```

### Complex Mapping Keys

```yaml
# Using lists or objects as keys
? - cardamom
  - ginger
  - cinnamon
: "Masala blend"

? {name: chai, type: beverage}
: "Hot drink made with tea and spices"
```

### Explicit Data Types

```yaml
# Force string interpretation
zip_code: !!str 12345
version: !!str 1.0

# Force integer
count: !!int "123"

# Force float
price: !!float "30"

# Set
unique_spices: !!set
  ? cardamom
  ? ginger
  ? cinnamon
```

### YAML Tags

```yaml
# Custom types
special_date: !date 2025-01-15
custom_object: !MyClass
  property: value
```

---

## Common Pitfalls

### 1. Tabs vs Spaces

```yaml
# ❌ WRONG - using tabs
chai:
	type: masala	# This will break!

# ✅ CORRECT - using spaces
chai:
  type: masala
```

### 2. Inconsistent Indentation

```yaml
# ❌ WRONG
chai_recipe:
  spices:
    - cardamom 
    - ginger  # Extra space!
  - cinnamon  # Wrong level!

# ✅ CORRECT
chai_recipe:
  spices:
    - cardamom
    - ginger
    - cinnamon
```

### 3. Special Characters in Unquoted Strings

```yaml
# ❌ WRONG
description: Chai & Coffee: The best!  # & and : are special

# ✅ CORRECT
description: "Chai & Coffee: The best!"
```

### 4. Boolean Confusion

```yaml
# These are ALL interpreted as booleans, not strings!
answer_yes: yes
answer_no: no
answer_on: on
answer_off: off

# If you want strings, quote them:
country_code: "no"  # Norway
response: "yes"
```

### 5. Number Interpretation

```yaml
# ❌ These might not be what you expect
phone: 9876543210      # Treated as number
version: 1.20          # Becomes 1.2 (trailing zero lost)
octal: 0123            # Interpreted as octal!

# ✅ CORRECT - quote them
phone: "9876543210"
version: "1.20"
code: "0123"
```

### 6. Empty Values

```yaml
# These are different!
value1:         # null/empty
value2: ""      # empty string
value3: null    # explicitly null
value4: ~       # null
```

---

## Real-World Examples

### 1. Chai Shop Configuration

```yaml
# config.yaml
chai_shop:
  name: "Chai Haven"
  location:
    address: "123 Tea Street"
    city: Mumbai
    country: India
    coordinates:
      lat: 19.0760
      lng: 72.8777
  operating_hours:
    monday_to_friday:
      open: "07:00"
      close: "22:00"
    weekend:
      open: "08:00"
      close: "23:00"
  menu:
    - id: 1
      name: "Classic Masala Chai"
      price: 30
      ingredients: [black_tea, milk, sugar, cardamom, ginger]
      available: true
    - id: 2
      name: "Adrak Chai"
      price: 35
      ingredients: [black_tea, milk, sugar, ginger]
      available: true
    - id: 3
      name: "Elaichi Chai"
      price: 35
      ingredients: [black_tea, milk, sugar, cardamom]
      available: false
  staff:
    - name: "Ravi Kumar"
      role: manager
      experience_years: 10
    - name: "Priya Sharma"
      role: barista
      experience_years: 3
```

### 2. CI/CD Pipeline (Chai Delivery App)

```yaml
# .github/workflows/chai-app.yml
name: Chai Delivery App CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '18'
  APP_NAME: chai-delivery

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
      - name: Install dependencies
        run: npm ci
      - name: Run tests
        run: npm test
      - name: Generate coverage
        run: npm run coverage

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Build Docker image
        run: |
          docker build -t ${{ env.APP_NAME }}:${{ github.sha }} .
      - name: Push to registry
        run: |
          docker push ${{ env.APP_NAME }}:${{ github.sha }}

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to production
        run: |
          kubectl set image deployment/${{ env.APP_NAME }} \
            app=${{ env.APP_NAME }}:${{ github.sha }}
```

### 3. Docker Compose (Chai Shop Microservices)

```yaml
# docker-compose.yml
version: '3.8'

services:
  # Frontend service
  chai-frontend:
    image: chai-shop/frontend:latest
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - REACT_APP_API_URL=http://localhost:8080
      - NODE_ENV=production
    depends_on:
      - chai-backend
    networks:
      - chai-network

  # Backend API service
  chai-backend:
    image: chai-shop/backend:latest
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgresql://chai_user:chai_pass@postgres:5432/chai_db
      - REDIS_URL=redis://redis:6379
      - JWT_SECRET=${JWT_SECRET}
    depends_on:
      - postgres
      - redis
    volumes:
      - ./logs:/app/logs
    networks:
      - chai-network

  # Database
  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_USER=chai_user
      - POSTGRES_PASSWORD=chai_pass
      - POSTGRES_DB=chai_db
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    networks:
      - chai-network

  # Cache
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - chai-network

volumes:
  postgres_data:
  redis_data:

networks:
  chai-network:
    driver: bridge
```

### 4. Kubernetes Deployment

```yaml
# k8s/chai-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: chai-delivery-app
  namespace: production
  labels:
    app: chai-delivery
    version: v1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: chai-delivery
  template:
    metadata:
      labels:
        app: chai-delivery
        version: v1
    spec:
      containers:
        - name: chai-app
          image: chai-shop/app:1.0.0
          ports:
            - containerPort: 8080
              name: http
          env:
            - name: DATABASE_URL
              valueFrom:
                secretKeyRef:
                  name: chai-secrets
                  key: database-url
            - name: REDIS_HOST
              value: "redis-service"
            - name: LOG_LEVEL
              value: "info"
          resources:
            requests:
              memory: "256Mi"
              cpu: "250m"
            limits:
              memory: "512Mi"
              cpu: "500m"
          livenessProbe:
            httpGet:
              path: /health
              port: 8080
            initialDelaySeconds: 30
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /ready
              port: 8080
            initialDelaySeconds: 5
            periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: chai-delivery-service
  namespace: production
spec:
  selector:
    app: chai-delivery
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer
```

### 5. Application Configuration

```yaml
# application.yaml
application:
  name: Chai Delivery Platform
  version: 1.0.0

server:
  port: 8080
  host: 0.0.0.0
  timeout:
    read: 30s
    write: 30s
    idle: 120s

database:
  primary:
    host: localhost
    port: 5432
    name: chai_db
    username: chai_user
    password: ${DB_PASSWORD}  # From environment variable
    pool:
      min: 5
      max: 20
      idle_timeout: 300
  replica:
    host: replica.localhost
    port: 5432
    name: chai_db
    username: chai_reader
    password: ${DB_REPLICA_PASSWORD}

cache:
  redis:
    host: localhost
    port: 6379
    db: 0
    ttl: 3600
    prefix: "chai:"

logging:
  level: info
  format: json
  outputs:
    - type: file
      path: /var/log/chai-app.log
      max_size: 100MB
      max_backups: 5
    - type: stdout

features:
  payment_gateway:
    enabled: true
    provider: stripe
    test_mode: false
  notifications:
    enabled: true
    channels:
      email: true
      sms: true
      push: true
  loyalty_program:
    enabled: true
    points_per_rupee: 1
    redemption_rate: 0.1

chai_menu:
  categories:
    - name: Classic
      items:
        - name: Masala Chai
          price: 30
          preparation_time: 5
        - name: Ginger Chai
          price: 35
          preparation_time: 5
    - name: Premium
      items:
        - name: Kashmiri Kahwa
          price: 80
          preparation_time: 8
        - name: Saffron Chai
          price: 100
          preparation_time: 10
```

---

## Best Practices

### 1. Use Consistent Indentation

```yaml
# Choose 2 or 4 spaces and stick with it
# 2 spaces (recommended)
chai:
  type: masala
  ingredients:
    - tea
    - milk
```

### 2. Quote Strings When Necessary

```yaml
# Quote strings with special characters
description: "Chai & Coffee: Best combo!"
url: "https://chai-shop.com"
code: "00123"
```

### 3. Use Meaningful Key Names

```yaml
# ❌ Not clear
c: masala
t: 5

# ✅ Clear and descriptive
chai_type: masala
brewing_time_minutes: 5
```

### 4. Keep It Simple

```yaml
# Avoid over-nesting when possible
# Instead of deep nesting, use flat structures with namespaced keys
chai_recipe_spice_cardamom: 2
chai_recipe_spice_ginger: 1
```

### 5. Use Comments Wisely

```yaml
# Document complex or non-obvious configurations
server:
  # Increase timeout for slow chai-brewing API endpoints
  timeout: 60
  # Connection pool sized for peak hours (500 concurrent users)
  pool_size: 100
```

### 6. Leverage Anchors for Reusability

```yaml
# Define common patterns once
default_settings: &defaults
  retry: 3
  timeout: 30

service_a:
  <<: *defaults
  endpoint: /api/v1

service_b:
  <<: *defaults
  endpoint: /api/v2
```

### 7. Validate Your YAML

- Use online validators: [yamllint.com](http://yamllint.com)
- Use CLI tools: `yamllint`, `yq`
- Enable YAML validation in your IDE

### 8. Use Version Control Friendly Format

```yaml
# Prefer block style for better diffs
dependencies:
  - package-a
  - package-b
  - package-c

# Instead of flow style
# dependencies: [package-a, package-b, package-c]
```

---

## Quick Reference Card

```yaml
# ☕ YAML Syntax Cheat Sheet

# Key-Value
key: value

# Strings
string: "quoted string"
multiline: |
  Line 1
  Line 2

# Numbers
integer: 42
float: 3.14

# Booleans
boolean: true

# Null
empty: null

# Lists
list:
  - item1
  - item2

# Dictionary
dict:
  key1: value1
  key2: value2

# Anchor & Alias
base: &anchor
  shared: data
use:
  <<: *anchor

# Comments
# This is a comment
```

---

## Practice Exercises

### Exercise 1: Create a Chai Recipe

Create a YAML file for your favorite chai recipe with:

- Name and description
- Ingredients (with quantities)
- Steps
- Metadata (prep time, servings, difficulty)

### Exercise 2: Configuration File

Design a YAML configuration for a chai ordering app with:

- Database settings
- API endpoints
- Feature flags
- Logging configuration

### Exercise 3: Docker Compose

Create a `docker-compose.yml` for a chai shop application with:

- Web frontend
- API backend
- Database
- Cache layer

---

## Tools and Resources

### Validators

- [YAML Lint](http://www.yamllint.com/) - Online validator
- [yamllint](https://github.com/adrienverge/yamllint) - CLI linter

### Parsers

- **Python**: PyYAML, ruamel.yaml
- **JavaScript**: js-yaml
- **Go**: [gopkg.in/yaml.v3](http://gopkg.in/yaml.v3)
- **Ruby**: psych (built-in)
- **Java**: SnakeYAML

### Editors with YAML Support

- VS Code (with YAML extension)
- IntelliJ IDEA
- Sublime Text
- Vim/Neovim (with yaml plugins)

---

## Summary

YAML is like making chai - it requires:

- ✅ **Precision** (indentation matters)
- ✅ **Balance** (structure and readability)
- ✅ **Quality ingredients** (proper data types)
- ✅ **Practice** (the more you write, the better you get)

Remember: Spaces, not tabs. Always validate. Keep it readable.

---

*Happy YAML-ing! ☕*