Cinema Tickets — Java Solution

Candidate/Application ID: 	17060809
Exercise: DWP Java & JavaScript Software Engineer — Coding Exercise

Overview

This project provides an implementation of a TicketService for a cinema ticket booking system.

The service is responsible for:

Validating ticket purchase requests against business rules
Calculating the total cost of tickets
Determining how many seats need to be reserved
Delegating payment and seat reservation to external services

The focus of the solution is on clean design, correctness, and testability.

Project Structure

-> The project follows a standard Maven layout with src/main/java and src/test/java.
-> The thirdparty package contains the provided external services:
  -> paymentgateway
     -> TicketPaymentService.java
     -> TicketPaymentServiceImpl.java
  -> seatbooking
    -> SeatReservationService.java
    -> SeatReservationServiceImpl.java
-> The main application code is under uk.gov.dwp.uc.pairtest:
  -> domain
    -> TicketTypeRequest.java (represents ticket requests)
  -> exception
    -> InvalidPurchaseException.java (handles invalid cases)
  -> TicketService.java (interface provided in the exercise)
  -> TicketServiceImpl.java (core implementation of the solution)
-> Unit tests are located under src/test/java:
   -> TicketServiceImplTest.java (covers all validation, calculation, and service interaction scenarios — 31 tests)
-> The root of the project includes:
    -> pom.xml for dependency management and build configuration
    -> README.md for documentation


Business Rules Implemented

The implementation enforces all rules defined in the exercise:

1. A maximum of 25 tickets can be purchased in a single request
2. At least one Adult ticket must be present when purchasing Child or Infant tickets
3. Infants are free and do not contribute to the total cost
4. Infants do not get a seat and must sit on an Adult’s lap
5. The number of Infants cannot exceed the number of Adults
6. The accountId must be non-null and greater than zero

Invalid requests result in an InvalidPurchaseException, and no external services are called.

Ticket Pricing

Ticket prices vary based on the type of ticket:

-> Adults are charged £25
-> Children are charged £15
-> Infants are free of charge


How It Works ?

-> The purchaseTickets method is the main entry point for handling ticket purchases.
-> It starts by validating the input:
  -> Checks if the accountId is valid
  -> Ensures the ticket request is not null or empty
-> It then applies all business rules:
   -> Maximum ticket limit (25)
   -> At least one adult ticket required
   -> Infant-to-adult ratio validation
-> Once validation passes, it performs calculations:
  -> Computes the total cost based on ticket types
  -> Determines the number of seats required (excluding infants)
-> After calculations:
  -> Calls the payment service to process the total amount
  -> Calls the seat reservation service to reserve the required seats
-> If any validation fails:
  -> An exception is thrown immediately
  -> No external services are called

Validation always happens first. If any rule is violated, the process stops immediately and no payment or reservation is attempted.

Design Decisions
Constructor Injection

TicketServiceImpl receives its dependencies via the constructor. This keeps the class loosely coupled and makes it easy to test using mocks.

Minimal Public API

Only purchaseTickets is exposed publicly. All helper logic (validation and calculations) is kept private to keep the API simple and focused.

Immutability
TicketTypeRequest has been made immutable by:

1. Marking the class as final
2. Making all fields final
3. Providing only getters

This ensures thread-safety and prevents accidental modification.

Handling Infant Seating

Since infants must sit on an adult’s lap, the solution enforces that:
number of infants ≤ number of adults

This is treated as a validation rule and results in an exception if violated.

Meaningful Exceptions

InvalidPurchaseException includes a message constructor so that failures are easier to understand and debug.

Running the Tests
Prerequisites
Java 21+
Maven 3.8+

Command
mvn test

Test Coverage

The solution includes a comprehensive test suite covering:

1. Account ID validation (null, invalid, valid cases)
2. Request validation (null inputs, invalid counts)
3. Business rules (adult requirement, ticket limits, infant constraints)
3. Payment calculation accuracy
4. Seat reservation logic
5. Correct interaction with external services

Total tests: 31 (all passing)

Example Usage:

TicketPaymentService paymentService = new TicketPaymentServiceImpl();
SeatReservationService reservationService = new SeatReservationServiceImpl();

TicketService ticketService = new TicketServiceImpl(paymentService, reservationService);

// 2 Adults, 3 Children, 1 Infant
// Cost  = £95
// Seats = 5
ticketService.purchaseTickets(
    123L,
    new TicketTypeRequest(Type.ADULT, 2),
    new TicketTypeRequest(Type.CHILD, 3),
    new TicketTypeRequest(Type.INFANT, 1)
);

Technologies Used
1. Java 21
2. Maven
3. JUnit 5
4. Mockito

Final Notes

The goal of this solution was not just to meet the requirements, but to keep the code clean, maintainable, and easy to reason about.
Particular attention was given to validation, separation of concerns, and test coverage.