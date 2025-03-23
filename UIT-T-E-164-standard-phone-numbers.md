# ITU-T E.164  
**ITU Recommendation for Numbering**

**Global Interoperability in Telecommunications**

Previous

Next

The ITU-T E.164 Recommendation is the main international standard for telephone numbering, developed by the International Telecommunication Union (ITU). Its objective is to ensure global interoperability in telecommunications by defining a uniform format for telephone numbers on public networks (PSTN) and other data networks.

## Contents of the E.164 Recommendation  
### Structure of E.164 Numbers

An E.164 number has a maximum of 15 digits and is divided into three main components:

| **Component**                                  | **Description**                                                                                         | **Example**                      |
|------------------------------------------------|---------------------------------------------------------------------------------------------------------|----------------------------------|
| **Country Code (CC)**                          | Identifies the country or region. It can have between 1 and 3 digits.                                   | +1 (USA), +54 (Argentina)        |
| **Destination Code**<br/>(national (NDC))       | Specifies jurisdictions within the country, such as geographic areas or specific services.              | 202 (Washington D.C.)            |
| **Subscriber Number (SN)**                     | Identifies the user within the national code. It can have up to 12 digits.                              | 5551234                          |

See the table in the corresponding figure.

## Categories of Numbers

The recommendation defines five categories of international numbers:

- **Geographic areas:** Numbers assigned to specific countries or regions with country codes and national numbers.
- **Global services:** Numbers assigned to specific international services, such as VoIP or satellite telecommunications.
- **Networks:** Numbers for private or corporate networks with unique codes within the standard.
- **Country groups:** Numbers shared among countries with integrated agreements (e.g., European Union).
- **Testing:** Temporary numbers assigned for tests and experiments in global telecommunications.

## Technical Aspects

- Numbers do not include international prefixes (00, 011, etc.), which are specific to each country.
- It allows the mapping of E.164 numbers to IP addresses through standards such as ENUM.

## Compliance by Country

Not all countries implement the E.164 recommendation uniformly due to regulatory, technical, and operational differences.

### United States  
Uses the country code +1 and strictly applies the E.164 format.  
Numbers include an area code (NDC) followed by the subscriber number.  
Example: +1 202 555 1234.  
The United States uses prefixes like 011 for international calls, but these are not part of the E.164 standard.

### Canada  
Also uses the +1 code, shared with the USA, but has its own area code scheme.  
Example: +1 416 555 1234 (Toronto).  
The implementation is similar to that in the United States, with differences in regional assignment.

### Spain  
Uses the country code +34 and strictly applies the E.164 format.  
Numbers do not have explicit area codes; the first digits of the national number indicate the region.  
Example: +34 912 345 678.  
Spain uses prefixes like 00 for international calls.

### Argentina  
Uses the country code +54. For mobile phones, an additional prefix (9) must be added before the national number.  
Example: +54 9 11 5050 6060 (Buenos Aires mobile).  
International prefixes such as 00 are common, but they are not part of the E.164 standard.

## Advantages of the Standard

- **Global interoperability:** It allows numbers to be recognized and used anywhere in the world without modifications.
- **Routing efficiency:** It facilitates proper routing and connection between international networks.
- **Conflict avoidance:** It ensures global uniqueness for each telephone number assigned under this scheme.

In conclusion, the ITU-T E.164 recommendation is essential for ensuring efficient and standardized international communication, although its implementation may vary according to local regulations and the technical capabilities of each country.

Not all countries strictly follow the E.164 format, and the reasons can vary depending on regulatory, technical, cultural, or economic issues. The following analyzes the countries that do not fully comply with this standard and the reasons behind these exceptions:

## Countries That Do Not Strictly Follow E.164

1. **Countries with Extra-Territorial Numbering**  
   Some countries allow the use of E.164 numbers outside their borders (extra-territorial use), which is not contemplated in the original standard.  
   *Examples:*  
   - San Marino and Vatican City: They use numbers from Italy’s national plan.  
   - Isle of Man: Uses numbers from the United Kingdom.  
   *Reason:* These small territories lack their own infrastructure and depend on bilateral agreements for number assignment.

2. **Countries with Open Numbering Plans**  
   In some countries, the numbering plans are not completely closed or structured according to E.164.  
   *Example:*  
   - Some African countries (such as Kenya and Morocco) have local formats that include national prefixes (0) in their international numbers.  
   *Reason:* Transitioning to a closed plan can be costly and complex, especially in emerging economies.

3. **Countries with Regulatory Restrictions**  
   Some countries impose specific restrictions that complicate the full implementation of E.164.  
   *Example:*  
   - In some CEPT (Conference of Postal and Telecommunications Administrations) member states, additional conditions such as physical presence or tax number are required to assign E.164 numbers to foreign companies.  
   *Reason:* These restrictions aim to protect local markets and ensure regulatory compliance.

## Main Reasons for Not Following E.164

- **Geographic restrictions:** Some countries assign numbers based on specific areas, limiting their international flexibility.
- **Extra-territorial use:** Allowing the permanent use of numbers in other countries can generate legal and technical issues.
- **Limited technical capabilities:** Developing countries face challenges in implementing systems compatible with E.164.
- **Open vs. closed plans:** In open plans, national prefixes (0) are part of the international number, which contradicts E.164.

## Country-Specific Implementation

- **United States/Canada:** Strictly comply. They use the shared code +1 with defined geographic area codes.
- **Spain:** Strictly complies, using the code +34 without national prefixes (0), fully aligned with E.164.
- **Argentina:** Partially complies since it requires an additional prefix 9 for mobiles in international calls, which is not part of E.164.
- **San Marino/Vatican City:** Do not strictly comply because they use numbers from the Italian plan (+39), which violates the territorial uniqueness defined by E.164.

The requirement to add an extra prefix 9 for calling mobiles in Argentina remains in effect in certain contexts, although there are initiatives to modernize and simplify the telephone numbering system.

## Current Situation in Argentina

- **Mobile prefix:**  
  - *From a landline:* The prefix 15 must be added before the mobile number for domestic calls.  
  - *From abroad:* To call an Argentine mobile, the prefix 9 must be added before the area code and then the mobile number.
- **Change Initiatives:**  
  The "Decile Chau al Prefijo 9 y 15" campaign seeks to eliminate these prefixes to simplify the numbering system and promote geographic and type portability. However, this initiative has not yet been implemented nationwide.
- **Current Status of the Obligation in Argentina:**  
  Although efforts are underway to modernize the system, the obligation to use the prefix 9 for international calls to Argentine mobiles remains in effect. The elimination of these prefixes could be part of future reforms to align the Argentine system with international standards such as E.164.

## Global Impact of Non-Compliance

- **Legal and regulatory issues:**  
  Extra-territorial use can generate conflicts over which laws to apply (e.g., Italy-San Marino).
- **Confusion for end users:**  
  Non-standardized formats complicate international dialing.
- **Unfair competition:**  
  The lack of harmonization can create barriers for foreign operators seeking to enter certain markets.

## Conclusion

Although most countries implement the E.164 format due to its importance for global interoperability, some do not fully adhere to it because of geographic, regulatory, or technical restrictions. Countries such as the United States and Spain strictly comply with the standard, while small territories like San Marino or emerging economies like Argentina present specific exceptions. This underscores the need for global harmonization to avoid technical and legal issues in international telecommunications.
