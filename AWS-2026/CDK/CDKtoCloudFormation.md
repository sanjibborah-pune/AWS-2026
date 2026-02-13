# AWS CDK: How C#/Python Code Becomes CloudFormation
 
This README explains what happens under the hood when you write AWS CDK apps in **C#** or **Python**.
 
---
 
## Does C#/Python CDK Code Convert Into TypeScript?
 
**No.**
 
Your CDK code is **not** translated into TypeScript source code.
 
The pipeline is NOT:
 
C# → TypeScript → CloudFormation
 
That is incorrect.
 
---
 
## The Real Engine Behind CDK: JSII
 
AWS CDK libraries are authored in **TypeScript**.
 
AWS uses a technology called **JSII** to generate bindings for other languages:
 
- Python  
- C#  
- Java  
- Go  
 
So when you write:
 
```csharp
new Bucket(this, "MyBucket");
```
 
or:
 
```python
s3.Bucket(self, "MyBucket")
```
 
You are calling the same underlying construct library through JSII.
 
---
 
## What Actually Happens When You Run CDK
 
When you run:
 
```bash
cdk synth
```
 
This is the real process:
 
---
 
### Step 1: Your Language Code Runs Normally
 
Python:
 
```bash
python app.py
```
 
C#:
 
```bash
dotnet run
```
 
Your program executes like any normal program.
 
---
 
### Step 2: Constructs Are Created in Memory
 
Your code builds a **construct tree**:
 
```
App
 └── Stack
      ├── S3 Bucket
      ├── IAM Role
      └── Lambda Function
```
 
Nothing is deployed yet.
 
---
 
### Step 3: CDK Synthesizes Into CloudFormation
 
CDK converts the construct tree into a CloudFormation template:
 
```yaml
Resources:
  MyBucket:
    Type: AWS::S3::Bucket
```
 
This template is the deployment artifact.
 
---
 
### Step 4: CloudFormation Deploys the Stack
 
When you run:
 
```bash
cdk deploy
```
 
CDK does:
 
1. Generate template  
2. Upload assets (Lambda zip, Docker image, etc.)  
3. Create or update CloudFormation stack  
 
CloudFormation performs the real provisioning.
 
---
 
## The Real Flow
 
```text
Your Code (Python/C#/Java)
        ↓ runs normally
Construct Tree (in memory)
        ↓ cdk synth
CloudFormation Template
        ↓ cdk deploy
CloudFormation Stack
        ↓
AWS Resources Created
```
 
---
 
## What Does NOT Happen
 
- Your code is NOT converted into TypeScript files  
- Your code is NOT transpiled  
- TypeScript is NOT an intermediate representation  
 
---
 
## Why This Matters
 
CDK is powerful because it lets you use full programming features:
 
- loops  
- conditions  
- abstractions  
- reusable modules  
 
Example:
 
```python
for env in ["dev", "prod"]:
    Bucket(self, f"Bucket-{env}")
```
 
CloudFormation alone cannot do this cleanly.
 
---
 
## Construct Levels (L1, L2, L3)
 
CDK provides multiple abstraction layers:
 
### L1: Raw CloudFormation
 
```python
CfnBucket(...)
```
 
### L2: AWS Abstractions
 
```python
s3.Bucket(...)
```
 
### L3: Higher-Level Patterns
 
```python
aws_s3_deployment.BucketDeployment(...)
```
 
All eventually synthesize into CloudFormation resources.
 
---
 
## Final Answer
 
- Does C#/Python CDK convert into TypeScript? **No**
- Does it become CloudFormation? **Yes**
- How? **JSII + Construct Tree + Synthesis**
 
---
 
## Next Architect-Level Question
 
If CDK produces CloudFormation anyway:
 
**What can CDK do that Terraform cannot?**
 
That comparison is career-defining.