# java-fuzzy-logic
Package provides java implementation of fuzzy logic controller


[![Build Status](https://travis-ci.org/cschen1205/java-fuzzy-logic.svg?branch=master)](https://travis-ci.org/cschen1205/java-fuzzy-logic) [![Coverage Status](https://coveralls.io/repos/github/cschen1205/java-fuzzy-logic/badge.svg?branch=master)](https://coveralls.io/github/cschen1205/java-fuzzy-logic?branch=master)


# Install

Add the following dependency into your POM file:

```xml
<dependency>
  <groupId>com.github.cschen1205</groupId>
  <artifactId>java-fuzzy-logic</artifactId>
  <version>1.0.1</version>
</dependency>
```

# Usage


Below is an example to create and run a fuzzy-logic-controller in java

```java
RuleInferenceEngine rie=new RuleInferenceEngine();

FuzzySet output=new FuzzySet("Output", -100, 100, 1);
output.addMembership("Cooling", new FuzzyReverseGrade(-50, 0));
output.addMembership("Zero", new FuzzyTriangle(-50, 0, 50));
output.addMembership("Heating", new FuzzyGrade(0, 50));
rie.addFuzzySet(output.getName(), output);

FuzzySet tempError=new FuzzySet("Temperature Error", -4, 4, 0.05);
tempError.addMembership("Negative", new FuzzyReverseGrade(-2, 0));
tempError.addMembership("Zero", new FuzzyTriangle(-2, 0, 2));
tempError.addMembership("Positive", new FuzzyGrade(0, 2));
rie.addFuzzySet(tempError.getName(), tempError);


FuzzySet tempErrorDot=new FuzzySet("Temperature Error", -10, 10, 0.1);
tempErrorDot.addMembership("Negative", new FuzzyReverseGrade(-5, 0));
tempErrorDot.addMembership("Zero", new FuzzyTriangle(-5, 0, 5));
tempErrorDot.addMembership("Positive", new FuzzyGrade(0, 5));
rie.addFuzzySet(tempErrorDot.getName(), tempErrorDot);

    /*
    1. If (e < 0) AND (er < 0) then Cool 0.5 & 0.0 = 0.0
    2. If (e = 0) AND (er < 0) then Heat 0.5 & 0.0 = 0.0
    3. If (e > 0) AND (er < 0) then Heat 0.0 & 0.0 = 0.0
    4. If (e < 0) AND (er = 0) then Cool 0.5 & 0.5 = 0.5
    5. If (e = 0) AND (er = 0) then No_Chng 0.5 & 0.5 = 0.5
    6. If (e > 0) AND (er = 0) then Heat 0.0 & 0.5 = 0.0
    7. If (e < 0) AND (er > 0) then Cool 0.5 & 0.5 = 0.5
    8. If (e = 0) AND (er > 0) then Cool 0.5 & 0.5 = 0.5
    9. If (e > 0) AND (er > 0) then Heat 0.0 & 0.5 = 0.0
    */

Rule rule=new Rule("Rule 1");
rule.addAntecedent(new Clause(tempError, "Is", "Negative"));
rule.addAntecedent(new Clause(tempErrorDot, "Is", "Negative"));
rule.setConsequent(new Clause(output, "Is", "Cooling"));
rie.addRule(rule);

rule=new Rule("Rule 2");
rule.addAntecedent(new Clause(tempError, "Is", "Zero"));
rule.addAntecedent(new Clause(tempErrorDot, "Is", "Negative"));
rule.setConsequent(new Clause(output, "Is", "Heating"));
rie.addRule(rule);

rule=new Rule("Rule 3");
rule.addAntecedent(new Clause(tempError, "Is", "Positive"));
rule.addAntecedent(new Clause(tempErrorDot, "Is", "Negative"));
rule.setConsequent(new Clause(output, "Is", "Heating"));
rie.addRule(rule);

rule=new Rule("Rule 4");
rule.addAntecedent(new Clause(tempError, "Is", "Negative"));
rule.addAntecedent(new Clause(tempErrorDot, "Is", "Zero"));
rule.setConsequent(new Clause(output, "Is", "Cooling"));
rie.addRule(rule);

rule=new Rule("Rule 5");
rule.addAntecedent(new Clause(tempError, "Is", "Zero"));
rule.addAntecedent(new Clause(tempErrorDot, "Is", "Zero"));
rule.setConsequent(new Clause(output, "Is", "Zero"));
rie.addRule(rule);

rule=new Rule("Rule 6");
rule.addAntecedent(new Clause(tempError, "Is", "Positive"));
rule.addAntecedent(new Clause(tempErrorDot, "Is", "Zero"));
rule.setConsequent(new Clause(output, "Is", "Heating"));
rie.addRule(rule);

rule=new Rule("Rule 7");
rule.addAntecedent(new Clause(tempError, "Is", "Negative"));
rule.addAntecedent(new Clause(tempErrorDot, "Is", "Positive"));
rule.setConsequent(new Clause(output, "Is", "Cooling"));
rie.addRule(rule);

rule=new Rule("Rule 8");
rule.addAntecedent(new Clause(tempError, "Is", "Zero"));
rule.addAntecedent(new Clause(tempErrorDot, "Is", "Positive"));
rule.setConsequent(new Clause(output, "Is", "Cooling"));
rie.addRule(rule);

rule=new Rule("Rule 9");
rule.addAntecedent(new Clause(tempError, "Is", "Positive"));
rule.addAntecedent(new Clause(tempErrorDot, "Is", "Positive"));
rule.setConsequent(new Clause(output, "Is", "Heating"));
rie.addRule(rule);

tempError.setX(-1.0);
logger.info("For Temperature Error: {}", tempError.getX());
logger.info("Negative: "+tempError.GetMembership("Negative").degree(-1.0));
logger.info("Zero: "+tempError.GetMembership("Zero").degree(-1.0));
logger.info("Positive: "+tempError.GetMembership("Positive").degree(-1.0));

tempErrorDot.setX(2.5);
logger.info("For Temperature Error Dot: {}", tempErrorDot.getX());
logger.info("Negative: "+tempErrorDot.GetMembership("Negative").degree(2.5));
logger.info("Zero: "+tempErrorDot.GetMembership("Zero").degree(2.5));
logger.info("Positive: "+tempErrorDot.GetMembership("Positive").degree(2.5));

rie.Infer(output);

logger.info("output: {} = {}", output.getName(), output.getX());
```