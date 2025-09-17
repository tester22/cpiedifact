# CPI to EDIFACT Converter

A comprehensive converter library for SAP Cloud Platform Integration (CPI) that provides seamless conversion between EDIFACT and XML formats using the [sapstern/edifactconverter](https://github.com/sapstern/edifactconverter) library.

## Features

- **EDIFACT to XML Conversion**: Convert EDIFACT messages to XML format
- **XML to EDIFACT Conversion**: Convert XML messages to EDIFACT format
- **Message Validation**: Built-in validation for both EDIFACT and XML messages
- **CPI Integration**: Designed specifically for SAP Cloud Platform Integration
- **Comprehensive Testing**: Full test coverage using Spock framework
- **Maven Support**: Easy integration with Maven-based projects

## Supported Message Types

- **ORDERS**: Purchase order messages (D.96A, D.01B)
- **INVOIC**: Invoice messages (D.96A, D.01B)
- **Extensible**: Easy to add support for additional message types

## Getting Started

### Prerequisites

- Java 8 or higher
- Maven 3.6 or higher
- SAP Cloud Platform Integration tenant (for deployment)

### Installation

1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-username/cpitoedifact-converter.git
   cd cpitoedifact-converter
   ```

2. **Build the project**:
   ```bash
   mvn clean install
   ```

3. **Run tests**:
   ```bash
   mvn test
   ```

### Usage

#### Basic Usage

```java
import com.cpitoedifact.converter.EdifactConverter;
import com.cpitoedifact.converter.EdifactConverterFactory;

// Create a converter instance
EdifactConverter converter = EdifactConverterFactory.createConverter();

// Convert EDIFACT to XML
String edifactMessage = "UNA:+.? '\\nUNB+UNOC:3+1234567890123:14+9876543210987:14+20240101:1234+1'\\nUNH+1+ORDERS:D:96A:UN'";
String xmlResult = converter.convertEdifactToXml(edifactMessage);

// Convert XML to EDIFACT
String xmlMessage = "<?xml version='1.0'?><ORDERS>...</ORDERS>";
String edifactResult = converter.convertXmlToEdifact(xmlMessage);
```

#### Using the Default Converter

```java
// Get the default singleton converter
EdifactConverter converter = EdifactConverterFactory.getDefaultConverter();

// Use the converter
String xmlResult = converter.convertEdifactToXml(edifactMessage);
```

#### Message Validation

```java
// Validate EDIFACT message
boolean isValidEdifact = converter.isValidEdifact(edifactMessage);

// Validate XML message
boolean isValidXml = converter.isValidXml(xmlMessage);
```

### CPI Integration

1. **Build the JAR file**:
   ```bash
   mvn clean package
   ```

2. **Deploy to CPI**:
   - Upload the generated JAR file to your CPI integration flow as an Archive
   - Configure your integration flow to use the converter

3. **Groovy Script Example**:
   ```groovy
   import com.cpitoedifact.converter.EdifactConverterFactory

   def converter = EdifactConverterFactory.getDefaultConverter()
   
   // Get the message payload
   def messageBody = message.getBody(String.class)
   
   // Convert based on message direction
   if (message.getHeaders().get("MessageDirection") == "EDIFACT_TO_XML") {
       def xmlResult = converter.convertEdifactToXml(messageBody)
       message.setBody(xmlResult)
   } else if (message.getHeaders().get("MessageDirection") == "XML_TO_EDIFACT") {
       def edifactResult = converter.convertXmlToEdifact(messageBody)
       message.setBody(edifactResult)
   }
   ```

## Configuration

The converter uses a Smooks configuration file (`smooks-config.xml`) to define supported message types and versions. You can customize this file to add support for additional EDIFACT message types.

### Adding New Message Types

Edit `src/main/resources/smooks-config.xml`:

```xml
<edifact:edifact-message-config>
    <edifact:message-type>NEWMESSAGE</edifact:message-type>
    <edifact:message-version>D</edifact:message-version>
    <edifact:message-release>96A</edifact:message-release>
</edifact:edifact-message-config>
```

## Testing

The project includes comprehensive unit tests using the Spock framework:

```bash
# Run all tests
mvn test

# Run tests with coverage
mvn clean test jacoco:report
```

### Test Data

Sample test data is provided in `src/test/resources/`:
- `sample-orders.xml` / `sample-orders.edifact`
- `sample-invoice.xml` / `sample-invoice.edifact`

## API Reference

### EdifactConverter Interface

```java
public interface EdifactConverter {
    String convertEdifactToXml(String edifactMessage) throws EdifactConversionException;
    String convertXmlToEdifact(String xmlMessage) throws EdifactConversionException;
    boolean isValidEdifact(String edifactMessage);
    boolean isValidXml(String xmlMessage);
}
```

### EdifactConverterFactory

```java
public class EdifactConverterFactory {
    public static EdifactConverter createConverter();
    public static EdifactConverter getDefaultConverter();
    public static void resetDefaultConverter();
    public static EdifactConverter createConverter(String configPath);
}
```

## Error Handling

The converter throws `EdifactConversionException` for conversion errors:

```java
try {
    String result = converter.convertEdifactToXml(edifactMessage);
} catch (EdifactConversionException e) {
    // Handle conversion error
    logger.error("Conversion failed: " + e.getMessage(), e);
}
```

## Dependencies

- **edifactconverter**: Core EDIFACT conversion library
- **Groovy**: CPI compatibility and testing
- **SLF4J**: Logging framework
- **Spock**: Testing framework

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- [sapstern/edifactconverter](https://github.com/sapstern/edifactconverter) - Core EDIFACT conversion functionality
- [engswee/equalize-cpi-converter](https://github.com/engswee/equalize-cpi-converter) - Inspiration for CPI converter architecture

## Support

For issues and questions:
- Create an issue in the GitHub repository
- Check the [documentation](docs/) for detailed guides
- Review the [test cases](src/test/) for usage examples
