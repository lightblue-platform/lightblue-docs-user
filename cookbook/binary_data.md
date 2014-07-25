# Binary Data
As of the writing of this encoding for binary data is up to the client.  This example uses base64 and is for a java client.

This is taken from the `BinaryTypeTest` class.

## Binary into JSON
```
import java.io.ByteArrayOutputStream;
import java.io.InputStream;
import javax.xml.bind.DatatypeConverter;
...

String pdfFilename = "./BinaryTypeTest-sample.pdf";

InputStream is = this.getClass().getClassLoader().getResourceAsStream(pdfFilename);

ByteArrayOutputStream buffer = new ByteArrayOutputStream();

int read;
byte[] bytes = new byte[1000];

while ((read = is.read(bytes, 0, bytes.length)) != -1) {
    buffer.write(bytes, 0, read);
}

String encoded = DatatypeConverter.printBase64Binary(buffer.toByteArray());

String jsonString = "{\"binaryData\": \"" + encoded + "\"}";
```

## Binary from JSON
```
import javax.xml.bind.DatatypeConverter;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.JsonNode;
...

String jsonString = "{\"binaryData\": \"... your encoded data ...\"}";

ObjectMapper mapper = new ObjectMapper();
mapper.configure(DeserializationFeature.USE_BIG_DECIMAL_FOR_FLOATS, true);

JsonNode node = mapper.readTree(s);

String encodedData = node.get("binaryData");

byte[] bytes = DatatypeConverter.parseBase64Binary(encodedData)
```
From here the data is back to the original bytes, do with it whatever is needed.
