# MoultonVolunteerForm

import 'package:flutter/material.dart';
import 'dart:convert';
import 'package:http/http.dart' as http;

void main() => runApp(VolunteerApp());

class VolunteerApp extends StatelessWidget {
  const VolunteerApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: WelcomeScreen(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class WelcomeScreen extends StatelessWidget {
  const WelcomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.white,
      body: Stack(
        children: [
          Row(
            children: [
              Container(width: 8, color: Colors.blue.shade900),
              Expanded(child: Container()),
              Container(width: 8, color: Colors.blue.shade900),
            ],
          ),
          Center(
            child: Padding(
              padding: const EdgeInsets.all(24.0),
              child: Column(
                mainAxisSize: MainAxisSize.min,
                children: [
                  Text(
                    "Use this form to record interactions with prospective and current volunteers. Contact information for prospective volunteers is preferred. Please be sure to submit one form for each individual. Thanks!",
                    style: TextStyle(
                      fontFamily: 'Calibri',
                      fontSize: 16,
                      color: Colors.black,
                    ),
                    textAlign: TextAlign.center,
                  ),
                  SizedBox(height: 24),
                  ElevatedButton(
                    onPressed: () {
                      Navigator.push(
                        context,
                        MaterialPageRoute(builder: (context) => SectionOneForm()),
                      );
                    },
                    child: Text("Begin"),
                  ),
                ],
              ),
            ),
          ),
        ],
      ),
    );
  }
}

class SectionOneForm extends StatefulWidget {
  const SectionOneForm({super.key});

  @override
  _SectionOneFormState createState() => _SectionOneFormState();
}

class _SectionOneFormState extends State<SectionOneForm> {
  final _formKey = GlobalKey<FormState>();
  final nameController = TextEditingController();
  final contactNameController = TextEditingController();
  final dateController = TextEditingController();
  final descriptionController = TextEditingController();
  bool hasAdditionalInfo = false;

  @override
  void dispose() {
    nameController.dispose();
    contactNameController.dispose();
    dateController.dispose();
    descriptionController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.white,
      body: Stack(
        children: [
          Row(
            children: [
              Container(width: 8, color: Colors.blue.shade900),
              Expanded(child: Container()),
              Container(width: 8, color: Colors.blue.shade900),
            ],
          ),
          SafeArea(
            child: SingleChildScrollView(
              padding: const EdgeInsets.all(24),
              child: Form(
                key: _formKey,
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.stretch,
                  children: [
                    Text("Section 1", style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold)),
                    SizedBox(height: 16),
                    TextFormField(
                      controller: nameController,
                      decoration: InputDecoration(labelText: "Your full name"),
                    ),
                    TextFormField(
                      controller: contactNameController,
                      decoration: InputDecoration(labelText: "Whom did you contact? (Full Name)"),
                    ),
                    TextFormField(
                      controller: dateController,
                      decoration: InputDecoration(labelText: "When did you connect?"),
                    ),
                    TextFormField(
                      controller: descriptionController,
                      decoration: InputDecoration(labelText: "Brief description of interaction:"),
                      maxLines: 4,
                    ),
                    SizedBox(height: 20),
                    Row(
                      children: [
                        Text("Do you have additional contact information?"),
                        Spacer(),
                        Switch(
                          value: hasAdditionalInfo,
                          onChanged: (value) {
                            setState(() {
                              hasAdditionalInfo = value;
                            });

                            if (value) {
                              Navigator.push(
                                context,
                                MaterialPageRoute(
                                  builder: (context) => SectionTwoForm(
                                    sectionOneData: {
                                      "name": nameController.text,
                                      "contact": contactNameController.text,
                                      "date": dateController.text,
                                      "description": descriptionController.text,
                                    },
                                  ),
                                ),
                              );
                            } else {
                              // TODO: Submit directly (we’ll handle this next)
                            }
                          },
                        ),
                      ],
                    ),
                  ],
                ),
              ),
            ),
          ),
        ],
      ),
    );
  }
}

  


class SectionTwoForm extends StatefulWidget {
  final Map<String, String> sectionOneData;

  const SectionTwoForm({super.key, required this.sectionOneData});

  @override
  _SectionTwoFormState createState() => _SectionTwoFormState();
}

class _SectionTwoFormState extends State<SectionTwoForm> {
  final emailController = TextEditingController();
  final phoneController = TextEditingController();
  final addressController = TextEditingController();
  String? volunteerLevel;

  final List<String> levels = ['A', 'B', 'C', 'D', 'X'];

  @override
  void dispose() {
    emailController.dispose();
    phoneController.dispose();
    addressController.dispose();
    super.dispose();
  }

  void _submitForm() {
    // Combine all form data
    final fullData = {
      ...widget.sectionOneData,
      "email": emailController.text,
      "phone": phoneController.text,
      "address": addressController.text,
      "volunteerLevel": volunteerLevel ?? '',
    };

_submitToGoogleSheets(fullData);

    // TODO: Add loading animation, submit to Google Sheets, and confirmation
Future<void> _submitToGoogleSheets(Map<String, String> data) async {
  const String url = https://script.google.com/macros/s/AKfycbyD5yjTtJPLi7lj3--kueBSUKdsQDNsOUAd4zFuPLqkaVZiNAq70JOWRxvdC1Z_eizCaA/exec;

  try {
    final response = await http.post(
      Uri.parse(url),
      headers: {'Content-Type': 'application/json'},
      body: jsonEncode(data),
    );

    if (response.statusCode == 200) {
      print("Submitted to Google Sheets!");
    } else {
      print("Failed to submit. Status: ${response.statusCode}");
    }
  } catch (e) {
    print("Error: $e");
  }
}

    showDialog(
      context: context,
      builder: (_) => AlertDialog(
        title: Text("Submitted!"),
        content: Text("Thank you."),
        actions: [
          TextButton(
            onPressed: () {
              Navigator.popUntil(context, (route) => route.isFirst);
            },
            child: Text("OK"),
          ),
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.white,
      body: Stack(
        children: [
          Row(
            children: [
              Container(width: 8, color: Colors.blue.shade900),
              Expanded(child: Container()),
              Container(width: 8, color: Colors.blue.shade900),
            ],
          ),
          SafeArea(
            child: SingleChildScrollView(
              padding: const EdgeInsets.all(24),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.stretch,
                children: [
                  Text("Section 2", style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold)),
                  SizedBox(height: 16),
                  TextFormField(
                    controller: emailController,
                    decoration: InputDecoration(labelText: "Email"),
                  ),
                  TextFormField(
                    controller: phoneController,
                    decoration: InputDecoration(labelText: "Phone number"),
                  ),
                  TextFormField(
                    controller: addressController,
                    decoration: InputDecoration(labelText: "Address"),
                  ),
                  SizedBox(height: 16),
                  DropdownButtonFormField<String>(
                    decoration: InputDecoration(labelText: "Volunteer Level"),
                    value: volunteerLevel,
                    items: levels
                        .map((level) => DropdownMenuItem(
                              value: level,
                              child: Text(level),
                            ))
                        .toList(),
                    onChanged: (value) {
                      setState(() {
                        volunteerLevel = value;
                      });
                    },
                  ),
                  SizedBox(height: 24),
                  ElevatedButton(
                    onPressed: _submitForm,
                    child: Text("Submit"),
                  ),
                ],
              ),
            ),
          ),
        ],
      ),
    );
  }
}

