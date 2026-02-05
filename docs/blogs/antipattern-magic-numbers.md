---
layout: default
title: "Anti-pattern: Magic Numbers"
parent: Blogs
nav_order: 2
---

# Anti-pattern: Magic Numbers

![Lighten Cognitive Load](../../static/img/lighten-cognitive-load.png)

## Description and Objective

This piece was a contribution to the Platform Engineering Guild's initiative to set company-wide code and documentation quality standards. The goal is to explain why magic numbers are harmful for codebases and provide actionable recommendations for improvement. The writing process involved gathering existing code quality standards, adapting themes to a blog-like format, and collaborating with engineers across teams.

---

## In This Post

- [Why it's a Bad Pattern](#why-its-a-bad-pattern)
- [Examples of the Problem](#examples-of-the-problem)
- [Examples of the Best Patterns](#examples-of-the-best-patterns)
- [Summary](#summary)

---

## Why it's a Bad Pattern

1. **Confusion**

   One of the oldest rules of programming, and dating back to the 1960s,
   [“magic numbers”](https://en.wikipedia.org/wiki/Magic_number_%28programming%29#:~:text=This%20has%20been%20referred%20to%20as%20breaking%20one%20of%20the%20oldest%20rules%20of%20programming)
   refer to hardcoded numbers without explanation. In most cases, magic numbers make programs harder to read and understand because they obscure the developers’ intent in choosing that specific number and/or value.
   Obscuring the intent can also contribute to unnecessary cognitive load; [Cognitive Load Theory](https://en.wikipedia.org/wiki/Cognitive_load) aka [“your brain’s tab limit”](https://guy-oxnb.medium.com/cognitive-load-why-its-destroying-your-code-and-what-to-do-about-it-3c28cc0d3598): one can only consider a certain amount of details at a time. If you “have too many tabs open” you’ll have a harder time completing your intended task.

2. **Errors**

   Magic numbers are error-prone because they can create unnecessary work and risk calculation mistakes.
   Hardcoding forces editing to be an overly-intricate task. If you’re forced to comb through the code to edit values in several spots, this may increase opportunities for typos, inconsistency, and bugs. If you’re hardcoding Pi (π) in several spots for some reason, is every single digit correct in every single spot? In addition, the value can be unintentionally rounded and or miscalculated in some way to create data inconsistencies.

3. **Poor Maintainability**

   Magic numbers pose similar risks described in
   [Anti-pattern: Lack of Documentation and Comments](antipattern-doc-comments.md)
   as they can demonstrate an absence of general alignment or code style standards. If your code is _confusing because it’s error-prone or error-prone because it’s confusing_, it’s less likely to adapt or receive future updates, and unintentionally break functionality while attempting updates.

   When maintainability becomes risky, it can accelerate further code degradation. If no one understands the meaning and or significance of these hardcoded values, they are more likely to be dismissed or untested while building features, and risk unintentionally negatively impacting business-critical services. These seemingly negligible unknowns can snowball into larger misalignment that follow your team throughout the
   [systems development life cycle](https://en.wikipedia.org/wiki/Systems_development_life_cycle).

---

## Examples of the Problem

1. **Standalone Calculations**
  
   The code below is performing some math to enable formatting, but why are we dividing by 10?
   Keeping this value at `10L` may leave everyone scratching their heads and prolong updates or debugging.

   ```java
   return NumberFormat.getInstance()
   .format(BigDecimal.valueOf(invoiceableRevenue)
   .divide(BigDecimal.valueOf(10L)
   .pow(currency.getDefaultFractionDigits()))) + "(" + symbol + ")";
   ```

2. **Stylistic Inconsistencies**

   Maybe it is not the magic number itself, but that the hardcoded value contributes to a larger pattern of inconsistency that risks perpetuating a cycle of poor maintainability. In the code below, observe that we have a mix of class properties and or constants, with test values that may not have any meaning to the intention of the test itself. This pattern may be unintentionally obscuring meaning and creating confusion.

   ```java
   @Test
    public void testHappyPath() {
        usageHandler.handle(TEST_USAGE_EVENT);
        final ArgumentCaptor<UsageEvent> captor = 
                  ArgumentCaptor.forClass(UsageEvent.class);
        verify(usageHandler).handle(captor.capture());
        final UsageEvent actualUsageEvent = captor.getValue();
        
        assertThat(actualUsageEvent).
            isEqualTo(ImmutableUsageEvent.builder()
                .setAccountId(ACCOUNT_ID)
                .setAmountLocal(123)
                .setProductId("test-productId")
                .setCurrency("USD")
                .setRequestId("TestUsageReporter-1")
                .setStartTime(Instant.parse("2025-01-01T12:00:00Z"))
                .setEndTime(Instant.parse("2025-01-01T12:01:00Z"))
                .setQuantity("10")
                .setUnitOfMeasure("COUNT")
                .setBillingReportFields("
                    [{\"key\":\"Job ID\",
                    \"value\":\"987\"},
                    {\"key\":\"Job Title\",
                    \"value\":\"Software Engineer II\"}]")
                .build());
   }
   ...
   ```

3. **Meaning vs Readability**

   Is it better to replace all magic numbers, none at all, or does it depend on the context? The code below features another test in which we have both a constant and hardcoded values. Does it matter that we see this mix in a test suite, does that make this any less risky? If we replace test value `"1"` with `REQUEST_ID_1`, this change might produce a different code smell. If our only naming differentiation is `_#` that might communicate that we either don’t understand why we’re using different values, or the values aren’t meaningful to the test beyond their presence. What would you keep and what would you replace with a constant? Consider what is more practical and what will create less future problems. We might end up sacrificing meaning for readability, or readability for meaning that can be supported elsewhere in the code.

   ```java
   @Test
    void testGetUninvoicedSumWithMonthBoundary() {
          usageDaoImpl.saveIfNotExist(
              ImmutableUsageEvent.copyOf(TEST_USAGE_EVENT)
                  .withRequestId("1").withAmountLocal(1));
         
          usageDaoImpl.saveIfNotExist(
              ImmutableUsageEvent.copyOf(TEST_USAGE_EVENT)
                           .withRequestId("2")
                           .withAmountLocal(2)
                           .withStartTime(DAY_ONE.minusSeconds(60*60*24)));

     ...<calls, assertion statements>...
   }
   ...
   ```

4. **Different Tools**

   If you’re using [IntelliJ](https://www.jetbrains.com/idea/), you may see some highlighting that reminds you that the first parameter is the request `status`, while the second parameter is the `invoicedRevenueId`, but what if IDE use is not standardized across your team? What if someone is working with this code or merely observing this code and not seeing the same highlighting? Different tools across teams might not be a reason to replace test values with constants, but consider standardizing test values across classes. [Places where magic numbers are appropriate](https://stackoverflow.com/questions/47882/what-are-magic-numbers-and-why-do-some-consider-them-bad#:~:text=Places%20where%20Magic%20Numbers%20are%20appropriate%20is%20everything%20that%20is%20defined%20through%20a%20standard) are where everything is defined through some kind of standard.

   ```java
   @Test
    public void testOutboxNotRunWhenLockNotAcquired() {
        insertPendingRequest("PENDING",123);
        insertPendingRequest("PENDING", 223);
        insertPendingRequest("RETRY", 323);
        insertPendingRequest("FAILED", 423);
   ...     
   ```

---

## Examples of the Best Patterns

1. **Constants Offer Meaning**

   The code below features a constant with a descriptive name. Using a constant instead of a hardcoded value takes the guesswork out of determining the significance of the value itself. In addition, if this description length needs to be adjusted, now we only need to edit it in one place, instead of manually editing it in every place it's referenced.

   Constant name length and or use of abbreviations might spark debate. `PJ` could be spelled out as `PROMOTED_JOBS`, but maybe this abbreviation is well understood, consistently adopted and or considered a team style standard. If it’s not a team standard, consider discussing and adopting it as such. With that being said, new team members will not have any such shared understanding. When possible, it is better to spell things out rather than use abbreviations or acronyms. Remember that clean code doesn’t always mean less code. Consider the practical choice and what will be easier to maintain by future-you and future team members.

   ```java
   public class InvoiceDescriptionHelper {
    private static final Logger LOGGER = 
        LoggerFactory.getLogger(InvoiceDescriptionHelper.class);
    
    private static final int PJ_MAX_DESCRIPTION_LENGTH = 400;

    ...<constructor, method declaration, definition>...

    if (productId == Product.PROMOTED_JOBS.getProductId()) {
        return getPromotedJobDescription(
               invoiceGrouping, 
               PJ_MAX_DESCRIPTION_LENGTH, 
               invoiceDate);
   }
   ...
   ```

2. **Consistency Offers Readability**

   In the code below we have a series of constants for test set-up. Again, we could have another conversation about meaningful naming and the limits therein. If the names themselves lean too generic, consider adjusting for something more descriptive, or offering more context in documentation, whether that be inline, with Javadoc comments, or elsewhere. At this moment, we might be sacrificing explicit meaning over readability. Observe how these constants offer blocks of consistency with standardization across tests, rather than what could be intermittent hardcoded values. Efforts that center readability often improve maintainability.

   ```java
   private static final long REQUEST_ID = 123456;
    private static final long REQUEST_ACCOUNT_ID = 5000098766L;
    private static final long REQUEST_PRODUCT_ID = 1;
    private static final long REQUEST_AMOUNT = 5500;
    private static final String REQUEST_CURRENCY = "USD";
    private static final BigDecimal REQUEST_TAX_RATE = 
                                          new BigDecimal(".125");
    private static final BigDecimal REQUEST_DISCOUNT_RATE = 
                                           new BigDecimal(".20");
    private static final String REQUEST_TAX_NAME = "VAT";
    private static final String REQUEST_ITEM_DESCRIPTION = "Request Test Description";
    private static final Timestamp REQUEST_DATE_CREATED = 
                             new Timestamp(new Date().getTime());
    private static final Timestamp REQUEST_INVOICE_DATE = 
                  new Timestamp(new Date(2024, 5, 15).getTime());

    @BeforeEach
    public void beforeEachTest() {
        MockitoAnnotations.openMocks(this);
        genericInvoice = 
                new GenericInvoiceWorker(genericInvoiceRequestsDao,
                invoiceServiceRpcClient,
                advertiserDataService);

        request = new GenericInvoiceRequest();
        request.setId(REQUEST_ID);
        request.setAccountId(REQUEST_ACCOUNT_ID);
        request.setProductId(REQUEST_PRODUCT_ID);
        request.setAmountLocal(REQUEST_AMOUNT);
        request.setCurrency(REQUEST_CURRENCY);
        request.setTaxName(REQUEST_TAX_NAME);
        request.setItemDescription(REQUEST_ITEM_DESCRIPTION);
        request.setDateCreated(REQUEST_DATE_CREATED);
   ...     
   ```

---

## Summary

1. **Center Readability**

   The point is not to nit-pick hardcoding, but when confronted with such, consider what is more readable. What is going to be easier to maintain during the next maintenance cycle? What is more readable will likely be more maintainable. In most cases, your magic number should be replaced by a symbolic constant, but there are also cases in which the numeric value is just fine.

2. **Lighten Cognitive Load**

   This guidance was specifically written for discussing good documentation practice, but applies to all types of code changes: _write for the engineer who has no context, which will include future-you, because future-you is not going to remember_. Try to eliminate habits that risk passing unnecessarily labor-intensive tasks to future-you and your teammates. You might be unintentionally doubling work by making changes that leave preventable research or require digging around for meaning each time it is referenced.

3. **You don’t need to bake from scratch**:

   - Seek out current best practices and recommendations
   - Harness the power of your IDE and don’t ignore the warnings
   - Leverage your IDE tools to automate inspections and fixes
