# DropDownbutton-using-streams-in-flutter
unable to load one drop down button upon selection of other dropdownbutton list item using streams in flutter 
problem in loading yardcodes... need help: I am using below code to display branchcode in one dropdown and upon selecting the branchcode. I must load yardcode in the other dropdown.. using API calls..(fetchBranchs(), fecthYards(ID)...
kindly help me.. As I do not understand where to invoke the event to load yardcode after selecting Branch code???


import 'dart:convert';
import 'dart:async';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'package:m_n_r/src/blocs/bloc.dart';
import 'package:m_n_r/src/blocs/provider.dart';
import 'package:m_n_r/src/models/usermodel.dart';
import 'package:m_n_r/src/screens/initScreen.dart';
import 'package:m_n_r/src/txtCss.dart';
import 'package:rxdart/rxdart.dart';

class LoginScreen extends StatefulWidget{

   createState() {
    // TODO: implement createState
    return LoginScreenHome();
  }
}

class LoginScreenHome extends State<LoginScreen>
{
  List<Branch> _branches = [];
  int selectedBranchID = 0;

  List<Yard> _yards = [];
  Yard selectedYard;

  bool isGotBranchs= false;

  Future<List<Branch>> fetchBranchs() async {
    //print('entered Fetch List user @ API ');
    final response = await http
          .get('http://dmsapi.logiconglobal.com/api/master/branch/branchlist/sct');
    if (response.statusCode == 200) {
      List branchs = json.decode(response.body);
      _branches = branchs.map((branch) => new Branch.fromJson(branch)).toList();
      return _branches;
    } else
      throw Exception('Failed to load Branch');
  }

  // sample to use incase of streams Stream<int> branchID argument
  Future<List<Yard>> fetchYards(branchID) async {
    var response = await http.get('http://dmsapi.logiconglobal.com/api/master/yard/branchyardlist/$branchID'); // + selectedBranch.branchID.toString() );
    if (response.statusCode == 200)
    { 
      List yards = json.decode(response.body);
      _yards = yards.map((yard) => new Yard.fromJson(yard)).toList();

      return _yards;
    } else
      throw Exception('Failed to load Yard');      
  }
  
  //Yard yard;
  //Branch branc;

  Widget build(context)
  {
    var bloc= Provider.of(context);
    var sizeHW = MediaQuery.of(context).size;
  
    TxtCss txtCss = new TxtCss();

    return new Stack(
      children: <Widget>[
        Row(
           mainAxisAlignment: MainAxisAlignment.start,
           crossAxisAlignment: CrossAxisAlignment.start,
           children:<Widget> [
            new Container(  // first row first column display pic
                width: ((62.5/100) * sizeHW.width),   // display the container width of 62.5% of orizinal width (old value:800.0),
                decoration: new BoxDecoration(
                image: new DecorationImage(
                   image: new AssetImage('lib/img/loginShip.png'),
                     fit: BoxFit.fill,
                  ),
                  //color: Colors.lightBlue,
                ),
            ),
            SingleChildScrollView(
              scrollDirection: Axis.vertical,
              child: //Expanded( flex: 10, child:   //<expanded not working>         
                new Container(
                    alignment: Alignment.topLeft,
                    width: ((37.5/100) * sizeHW.width), //480.0,
                    child: Column( 
                            mainAxisAlignment: MainAxisAlignment.start,
                            children: [
                              Container(padding: EdgeInsets.only(top:180.00,left:10.00), 
                                        alignment: Alignment.topLeft,
                                        child: Image.asset('lib/img/virgoleoSolutions.png'),),
                              Container(
                              alignment: Alignment.topLeft,
                              margin: const EdgeInsets.all(10.00),
                              child: Column( children: [
                                SizedBox(height:50.00),
                                Row(children:<Widget>[Expanded(child: uidField(bloc,txtCss),)]), 
                                Row(children:<Widget>[Expanded(child: passwordField(bloc,txtCss))]), 
                                SizedBox(height:25.00),
                                Row(children:<Widget>[Expanded(child: branchCodeList(bloc,txtCss))]),                               
                                SizedBox(height:20.00),
                                Row(children:<Widget>[Expanded(child: yardCodeList(bloc,txtCss))]),
                                SizedBox(height:25.00),                                                                                                   
                                Row(children:<Widget>[Expanded(child: submitButton(bloc,txtCss))]),
                                //SizedBox(height:15.00),                                
                                // Row(
                                //   children: <Widget>[
                                //     Expanded(child: Text('Forgot password?',style: txtCss.txtRoboStyle(14.0), textAlign: TextAlign.right,))
                                //   ],
                                // ),

                                // sample code to display height and width of the Screen
                                // Row(
                                //   children: <Widget>[
                                //     Expanded(child: Text('Height: ' + sizeHW.height.toString(),style: TextStyle(fontSize: 20.0, color: Colors.brown),)),
                                //     Expanded(child: Text('Height: ' + ((31.25/100) * sizeHW.width).toString() + ' :- ' + sizeHW.height.toString(),style: TextStyle(fontSize: 20.0, color: Colors.brown),)),
                                     
                                //     Container(width:110.00),
                                //     Expanded(child: Text('width: ' + sizeHW.width.toString(),style: TextStyle(fontSize: 20.0, color: Colors.brown),))
                                //   ],
                                // ),
                                
                                //Container(margin: EdgeInsets.only(top: 20.0)),
                                 
                                  
                                ])
                              )
                            ],
                          )
                )
            )
          ],
      ),
    ]
    );
  }

  Widget uidField(Bloc bloc, TxtCss txtSS)
  {
    return StreamBuilder(
      stream: bloc.eMail,
      builder: (context, snapshot) 
        { 
          return TextField(
            style: txtSS.txtRoboStyle(20),
            onChanged: bloc.changeEmail,
            keyboardType: TextInputType.emailAddress,
            textInputAction: TextInputAction.next ,
            decoration: InputDecoration(
            labelStyle: txtSS.txtRoboStyle(20.00),
            hintText: 'user id',
            labelText: 'User:',
            errorText: snapshot.error,
            ),
          );
        }
      
      );
    
  }

  Widget passwordField(Bloc bloc, TxtCss txtSS)
  {
    return StreamBuilder(
      stream: bloc.passWord,
      builder: (context, snapshot)
        {
          return TextField(
            style: txtSS.txtRoboStyle(20.00),
            onChanged: bloc.changePassword,
            textInputAction: TextInputAction.next,
            obscureText: true,
            decoration: InputDecoration(
              labelStyle: txtSS.txtRoboStyle(20.00),
              hintText: 'password',
              labelText: 'Password:',
              errorText: snapshot.error,
            ),
          );     
        },
    );
  }

  Widget branchCodeList (Bloc bloc, TxtCss txtSS)
  {
    return StreamBuilder(
      stream: bloc.branchCode,
      builder: (context, snapshot) 
        { 
          return FutureBuilder(
            future: fetchBranchs(),
            builder: (context, branchsData)
            {
              return branchsData.hasData ? 
                DropdownButton<String>(
                  isDense: true,
                  items: branchsData.data
                    .map<DropdownMenuItem<String>>(
                      (Branch dropDownStringitem){ 
                        return DropdownMenuItem<String>(
                          value: dropDownStringitem.branchID.toString(), 
                          child: Text(dropDownStringitem.branchCode, 
                                  style: txtSS.txtRoboStyle(20),),);
                      },
                ).toList(),
                  
                onChanged: bloc.changeBranch, 
                value: snapshot.data,
                // onChanged: (val) { setState((){  selectedBranchID = int.parse(val);});  },
                // value: selectedBranchID.toString(),
                //value: bloc.getBrCode(),
                style: txtSS.txtRoboStyle(20.00),
                hint: Text('Branch Code '),
                elevation: 8,
              )
            :
              CircularProgressIndicator();
            },
          );
        }
    );
  }


  Widget yardCodeList(Bloc bloc, TxtCss txtSS) 
  {
    return StreamBuilder(
      stream: bloc.yardCode,  
      builder: (context, snapshot) 
        {
          bloc.branchCode.listen((onData) => selectedBranchID = int.parse(onData));
          return FutureBuilder(
            future: fetchYards(selectedBranchID), //fetchYards(getBrCode(bloc))
            builder: (context, yardsData){
              if (yardsData.hasData)
              {
                return DropdownButton<String>(
                  items: yardsData.data.map<DropdownMenuItem<String>>(
                          (Yard dropDownStringitem) {
                            return DropdownMenuItem<String>(
                                  value: dropDownStringitem.yardCode,
                                  child: Text(dropDownStringitem.description,
                                          style: txtSS.txtRoboStyle(20),)
                                  );
                          },
                        ).toList(),
                
                  onChanged: bloc.changeYard, 
                  value: snapshot.data,
                  hint: Text('Yard Code '),
                  style: txtSS.txtRoboStyle(20),
                  elevation: 8,
                );
              }
              else
              {
                return CircularProgressIndicator();
              }
            }
          );
        }
      );
  }

  Widget submitButton(Bloc bloc, TxtCss txtSS)
  {
    return StreamBuilder(
      stream: bloc.submitValid,
      builder: (context, snapshot) 
      {
        return RaisedButton(
          child: Text('Sign In',style: txtSS.btnTxtRoboStyle(20),),
          color: Colors.indigo,
          disabledColor: Colors.red,
          textColor: Colors.white,

          //shape: ,         
          onPressed: !snapshot.hasData ? null : () 
                                { //bloc.submit();
                                  Navigator.push(context, new MaterialPageRoute(
                                  builder: (context) =>
                                    new InitScreen())
                                  );
                                },
        );
      },  
    );

  }

}
