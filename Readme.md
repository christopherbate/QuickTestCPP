## QuickTestCPP

QuickTestCPP is a lightweight header-only tool for quickly developing unit tests in C++ (11). I created this to for projects that requried unit tests, but for whatever reason bringing in a larger testing library was out of the question. I modeled the terminology after Google Tests's terminology. A "test" is an individual test, and a "test case" is a set of tests. We keep the syntax relatively lightweight by leveraging C++ lambda functions. The whole idea is relatively simple, as shown in the below examples. Create the tests with lambda functions, and return true or 1 on success, 0 or false on failure. At the end, call the print summary for a description. 

## Example usage.


~~~~
#include "QuickTestCPP.h"
#include <iostream>
[other includes]

int main(int argc, char **argv)
{
    std::cout << "Starting tests." << std::endl;

    TestRunner::GetRunner();

    TestRunner::GetRunner()->AddTest(
        "HTTP Request Parsing",
        "Must parse protocol version",
        []() {
            string hdr = "GET /Protocols/rfc1945/rfc1945 HTTP/1.1";
            HTTPRequest request(hdr);
            if (request.GetProtocol() != "HTTP/1.1")
                return 0;
            return 1;
        });

    TestRunner::GetRunner()->AddTest(
        "HTTP Request Parsing",
        "Must parse url",
        []() {
            string hdr = "GET /Protocols/rfc1945/rfc1945 HTTP/1.1";
            HTTPRequest request(hdr);
            if (request.GetUrl() != "/Protocols/rfc1945/rfc1945")
                return 0;
            return 1;
        });

    TestRunner::GetRunner()->AddTest(
        "TCP Socket Creation",
        "Must be able to accept connections",
        []() {
            TCPSocket socket;

            bool res = socket.CreateSocket("8080");
            if (!res)
            {
                std::cerr << "Failed to create list socket." << endl;
                return 0;
            }

            socket.Listen();

            TCPSocket connSocket;
            res = connSocket.CreateSocket("localhost", "8080");
            if (!res)
            {
                std::cerr << "Failed to create active socket." << endl;
                return 0;
            }

            TCPSocket *connection = socket.Accept();

            if(connection == NULL){
                std::cerr <<"Did not accept connection"<<endl;
                return 0;
            }

            delete connection;

            connSocket.ISocket::CloseSocket();
            socket.ISocket::CloseSocket();
            return 1;
        });

    TestRunner::GetRunner()->Run();

    TestRunner::GetRunner()->PrintSummary();

    return 0;
}
~~~~
