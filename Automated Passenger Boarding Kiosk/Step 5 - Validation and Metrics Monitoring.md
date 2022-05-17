# Before Validation
All initial validation status values are set to FALSE in the flight manifest table

# Validation
## NameValidation
>The first and last name extracted from the boarding pass and ID card must match with the name on the flight manifest table

## DoBValidation
>DoB extracted from the ID card match with the flight manifest table

## BoardingPassValidation
>The flight-specific information extracted from the boarding pass is matched with the flight manifest table. The information includes 
>* flight number
>* seat number
>* class
>* origin
>* destination
>* flight date
>* flight time

## PersonValidation
>Face extracted from the ID and that from the video should match, and the match result should be 65% or higher.

Here I got 81.3% as shown below

[![faceverify.png](https://i.postimg.cc/Zq0MY0NV/faceverify.png)](https://postimg.cc/mcfw8L8H)

## LuggageValidation
>The carry-on loose items in the passenger's pocket contain a lighter in it or not

All the luggages contain a lighter.

## Conclusion
Most of the validation details are `TRUE` now
