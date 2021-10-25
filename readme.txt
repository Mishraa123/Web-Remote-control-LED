#include <SPI.h>
#include <Ethernet.h>

byte mac[] = { 0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED }; //physical mac address
byte ip[] = { 192, 168, 0, 150 }; // IP address in LAN – need to change according to your Network address
byte subnet[] = { 255, 255, 255, 0 }; //subnet mask beacuse this Class C IP
EthernetServer server(80); // This is server port address
String readString;
int ledPin = 2;// LED conntected with Arduino pin 2
void setup(){ // this function run only on time
    pinMode(ledPin, OUTPUT); //pin selected to control
    //start Ethernet
    Ethernet.begin(mac, ip,  subnet);
    server.begin();
}

void loop(){
    // Create a client connection
    EthernetClient client = server.available();
    if (client) {
        while (client.connected())
          {
            if (client.available()) {
                char c = client.read();

                //read char by char HTTP request
                if (readString.length() < 100) {

                    //store characters to string
                    readString += c;
                }

                //if HTTP request has ended– 0x0D is Carriage Return \n ASCII
                if (c == '\n') {
                    client.println("HTTP/1.1 200 OK"); //send new page
                    client.println("Content-Type: text/html");
                    client.println();

                    client.println("<HTML>");
                    client.println("<HEAD>");
                    client.println("<TITLE> ARDUINO ETHERNET SHIELD</TITLE>");
                    client.println("</HEAD>");
                    client.println("<BODY>");
                    client.println("<hr>");
                    client.println("<hr>");
                    client.println("<br>");
                    client.println("<H1 style=\"color:green;\">ARDUINO ETHERNET SHIELD — LED ON/OFF FROM WEBPAGE</H1>");
                    client.println("<hr>");
                    client.println("<br>");

                    client.println("<H2><a href=\"/?LEDON\"\">Turn On LED</a><br></H2>");
                    client.println("<H2><a href=\"/?LEDOFF\"\">Turn Off LED</a><br></H2>");

                    client.println("</BODY>");
                    client.println("</HTML>");
                    // delay denotes time in ms
                    delay(10);
                    //stopping client
                    client.stop();

                    // control arduino pin
                    if(readString.indexOf("?LEDON") > -1) //checks for LEDON
                    {
                        digitalWrite(ledPin, HIGH); // set pin high
                    }
                    else{
                        if(readString.indexOf("?LEDOFF") > -1) //checks for LEDOFF
                        {
                            digitalWrite(ledPin, LOW); // set pin low
                        }
                    }
                    //clearing string for next read
                    readSt
                    ring="";

                }
            }
        }
    }
}
