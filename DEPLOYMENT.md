# CPI to EDIFACT Converter - Deployment Guide

## JAR Files Generated

The build process creates two JAR files in the `target/` directory:

### 1. Standard JAR (`cpitoedifact-converter-1.0.0.jar`)
- **Size**: ~8.5 KB
- **Purpose**: Contains only the converter classes
- **Dependencies**: Requires all dependencies to be available in CPI runtime
- **Use Case**: When you want to manage dependencies separately

### 2. Fat JAR (`cpitoedifact-converter-1.0.0-fat.jar`)
- **Size**: ~32 MB
- **Purpose**: Contains all dependencies bundled together
- **Dependencies**: Self-contained, no external dependencies needed
- **Use Case**: **Recommended for CPI deployment** - easier to manage

## CPI Deployment Steps

### Option 1: Using Fat JAR (Recommended)

1. **Upload the Fat JAR**:
   - Use `cpitoedifact-converter-1.0.0-fat.jar` from the `target/` directory
   - Upload as an Archive in your CPI integration flow

2. **Configure Integration Flow**:
   - Add a Groovy Script step
   - Use the converter as shown in the examples below

### Option 2: Using Standard JAR

1. **Upload Dependencies**:
   - Upload `cpitoedifact-converter-1.0.0.jar`
   - Upload all required dependencies (see dependency list below)

2. **Configure Integration Flow**:
   - Add all JARs as Archives
   - Use the converter in your Groovy scripts

## Groovy Script Examples

### Basic Usage in CPI

```groovy
import com.cpitoedifact.converter.EdifactConverterFactory

// Get the converter instance
def converter = EdifactConverterFactory.getDefaultConverter()

// Get the message payload
def messageBody = message.getBody(String.class)

// Convert based on message direction
if (message.getHeaders().get("MessageDirection") == "EDIFACT_TO_XML") {
    def xmlResult = converter.convertEdifactToXml(messageBody)
    message.setBody(xmlResult)
    message.setHeader("ContentType", "application/xml")
} else if (message.getHeaders().get("MessageDirection") == "XML_TO_EDIFACT") {
    def edifactResult = converter.convertXmlToEdifact(messageBody)
    message.setBody(edifactResult)
    message.setHeader("ContentType", "application/edifact")
}
```

### Error Handling

```groovy
import com.cpitoedifact.converter.EdifactConverterFactory
import com.cpitoedifact.converter.EdifactConversionException

try {
    def converter = EdifactConverterFactory.getDefaultConverter()
    def result = converter.convertEdifactToXml(message.getBody(String.class))
    message.setBody(result)
} catch (EdifactConversionException e) {
    // Handle conversion errors
    message.setHeader("CamelExceptionCaught", e)
    message.setBody("Conversion failed: " + e.getMessage())
}
```

## Required Dependencies (for Standard JAR)

If using the standard JAR, ensure these dependencies are available:

- `org.milyn:milyn-smooks-all:1.7.0`
- `org.codehaus.groovy:groovy-all:2.4.4`
- `javax.xml.bind:jaxb-api:2.3.1`
- `org.slf4j:slf4j-api:1.7.32`

## Testing the Deployment

1. **Create Test Messages**:
   - Use the sample files from `src/test/resources/`
   - Test both EDIFACT to XML and XML to EDIFACT conversions

2. **Verify Conversion**:
   - Check that the output format is correct
   - Validate that error handling works as expected

## Troubleshooting

### Common Issues

1. **ClassNotFoundException**:
   - Ensure all dependencies are uploaded as Archives
   - Use the fat JAR instead of the standard JAR

2. **Conversion Errors**:
   - Check that input messages are valid EDIFACT or XML
   - Verify message format matches supported types (ORDERS, INVOIC)

3. **Memory Issues**:
   - The fat JAR is large (~32MB) but self-contained
   - Consider using standard JAR if memory is a concern

### Logging

The converter uses SLF4J for logging. Check CPI logs for:
- Conversion start/completion messages
- Error details and stack traces
- Validation warnings

## Support

For issues and questions:
- Check the main README.md for detailed documentation
- Review the test cases in `src/test/` for usage examples
- Create an issue in the GitHub repository
