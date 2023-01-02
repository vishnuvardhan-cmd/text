import { rgbToHex } from "@material-ui/core";
import { Container, Row, Col } from 'react-bootstrap';
import React, { useEffect, useState } from "react";
import axios from 'axios';
// import CompareJSONModal from "./CompareJSONModal";
import ViewApiScripts from "./ViewApiScripts";
function ApiScripts() {
    const [val, setVal] = useState([])
    const [showText, setShowText] = useState(false)
    const [viewData, setViewData] = useState(false)
    const [data, setData] = useState('')
    const handleAdd = () => {
        setShowText(prev => !prev)
    }
    const handleDataChange = () => {
        if (data !== '') {
            let changeData = [...val, data]
            setVal(changeData)
            setData('')
        }
    }
    const handleChange = (changeValue, i) => {
        let data = changeValue.target.value;
        setData(data)
    }
    const handleDelete = (i) => {
        const deleval = [...val]
        deleval.splice(i, 1)
        setVal(deleval)
    }
    const styleObj = {
        display: "grid",
        placeItems: "center",
        backgroundColor: "rgbToHex(12,34,45)",
    }
    const colObj = {
        placeItems: "center"
    }
    const rowObj = {
        paddingTop: "20px",
        display: "flex",
        marginBottom: "2px",
    }

    const fileUpload = (event) => {
        event.preventDefault();
        const reader = new FileReader();
        reader.onload = (e) => {
            const text = e.target.result;
            let inputData = [...val]
            inputData = [...inputData, text]
            setVal(inputData)
        }
        reader.readAsText(event.target.files[0])

    }
    const sendData = () => {
        console.log("I am in...")
        console.log(val)
        let newData = val.map(v => { return { "query": v } })
        const content = {
            "data": {
                "queryList": newData
            }
        }
        setVal([])
        axios({
            url: "http://localhost:8080/scriptExecutor",
            method: "post",
            data: content,
            timeout: 30000,
            headers: {
                "Content-Type": "application/json",
            }
        }).then(response => {
            const { data } = response;
            console.log(data)
        }).catch(e => {
            console.log(e);
        });

    }
    useEffect(() => {
        // let dt = val.map(v => JSON.parser(v))
        // setVal(dt)
        // console.log(val)
        console.log(val)
    }, [val])
    return (
        <div>
            <div style={{
                display: 'flex', flexDirection: 'column', margin: '15px 40px 35px', boxShadow: 'rgb(0 0 0 / 25%) 3px 3px 5px 3px',
                padding: '20px',
                zindex: '1030',
                overflowX: 'auto'
            }}>
                <Container style={colObj}>
                    <Row className="justify-content-md-center" style={rowObj}>
                        <Col>Api Type Migration</Col>
                        <Col>
                            <button>csv</button>&nbsp;&nbsp;
                            <button>Form</button>
                        </Col>
                        <Col>
                            <button onClick={() => handleAdd()}>Add&nbsp;+</button>
                        </Col>
                        <Col>
                            <div className='file-upload'>
                                <div className='file-select'>
                                    <input id="file-data" type="file" className='file-upload' onChange={(e) => fileUpload(e)} />
                                </div>
                            </div>
                        </Col>
                    </Row>
                </Container>
                <hr />
                {showText && <center>
                    <textarea placeholder="Enter the sql script(insert,update,delete)" rows="6" cols="100" value={data} onChange={e => handleChange(e)} />
                    {/*  */}
                    <button onClick={() => handleDataChange()}>+</button>
                    <br></br>
                </center>}
                <center><button type="submit" onClick={() => sendData()} style={{ width: "30%", }}>Submit</button></center>
            </div>
            <hr />
            <hr />
            <center>
                <div>
                    {
                        val.map((data1, i) => {
                            return (
                                <div>
                                    <textarea style={{pading:'20px',width:'60%',height:'8.5em',overflow:"scroll"}} value={data1} onChange={e => handleChange(e, i)} />
                        
                                    <button onClick={() => handleDelete(i)}>x</button>
                                    <br/>
                                    <br></br>
                                </div>
                            )
                        })
                    }
                </div>
            </center>
        </div>
    )
}

export default ApiScripts;

response:

{
    "meta": {
        "timestamp": "2022-12-30T11:11:02Z",
        "cxpCorrelationId": "2022-12-30T16:41:02.+0530:17b6fe3990f9b6cc:VIN0019ALKKWEUE:payload:local"
    },
    "data": {
        "scriptExecutorResponse": [
            {
                "querNo": 1,
                "status": "SUCESS",
                "affectedRows": 1
            },
            {
                "querNo": 1,
                "status": "FAILURE",
                "affectedRows": 0
            }
        ]
    }
}

import React, { useEffect } from 'react';
import { Table } from 'react-bootstrap/Table';
import { useLocation } from 'react-router-dom';
import { dataSyncApi } from '../../service';

function ViewScripts() {
    const location = useLocation();
    // useEffect(() => console.log(location.state.data))
    const data=JSON.parse(location.state.data);
    return (
        <Table striped bordered hover>
            <thead>
                <tr>
                    <th>#</th>
                    <th>QueryNo</th>
                    <th>Status</th>
                    <th>AffectedRows</th>
                </tr>
            </thead>
            <tbody>
                {
                    data.map((ele,i) => {
                        return (
                            <tr>
                                <td>{data.scriptExecutorResponse[i].queryNo}</td>
                                <td>{data.scriptExecutorResponse[i].status}</td>
                                <td>{data.scriptExecutorResponse[i].affectedRows}</td>
                            </tr>
                        )
                    })}
            </tbody>
        </Table>
    );
}

export default ViewScripts;



{
  "name": "cxp_logger_console",
  "version": "0.1.0",
  "private": true,
  "proxy": "https://onejira.verizon.com/rest/api/2/",
  "dependencies": {
    "@appbaseio/reactivesearch": "^3.1.1",
    "@fortawesome/fontawesome-svg-core": "^1.2.28",
    "@fortawesome/free-solid-svg-icons": "^5.13.0",
    "@fortawesome/react-fontawesome": "^0.1.9",
    "@material-ui/core": "^4.9.8",
    "@okta/okta-react": "^1.3.1",
    "@vx/gradient": "0.0.195",
    "@vx/group": "0.0.195",
    "@vx/hierarchy": "0.0.195",
    "@vx/responsive": "0.0.195",
    "@vx/shape": "0.0.195",
    "@vx/text": "0.0.195",
    "@vzrf/react": "^1.10.0",
    "apollo-cache-inmemory": "^1.6.5",
    "apollo-client": "^2.6.8",
    "apollo-link-http": "^1.5.16",
    "axios": "^0.19.0",
    "bootstrap": "^4.3.1",
    "chart.js": "^2.9.3",
    "d3-hierarchy": "^1.1.9",
    "dateformat": "^4.4.1",
    "elasticsearch": "^16.5.0",
    "express": "^4.17.3",
    "fast-json-patch": "^3.1.1",
    "font-awesome": "^4.7.0",
    "graphql": "^14.6.0",
    "graphql-tag": "^2.10.3",
    "http-proxy-middleware": "^2.0.4",
    "immer": "^3.1.3",
    "jest-fetch-mock": "^3.0.3",
    "jsonpath": "^1.1.1",
    "luxon": "^1.25.0",
    "moment": "^2.24.0",
    "moment-timezone": "^0.5.32",
    "react": "^16.11.0",
    "react-apollo": "^3.1.3",
    "react-bootstrap": "^1.0.0-beta.14",
    "react-bootstrap-table": "^4.3.1",
    "react-bootstrap-table-next": "^3.3.1",
    "react-bootstrap-table2-paginator": "^2.1.0",
    "react-chartjs-2": "^2.8.0",
    "react-copy-to-clipboard": "^5.0.3",
    "react-d3-tree": "^1.16.1",
    "react-data-export": "^0.5.0",
    "react-datetime-picker": "^2.7.1",
    "react-dom": "^16.11.0",
    "react-icons": "^3.8.0",
    "react-json-pretty": "^2.2.0",
    "react-json-view": "^1.19.1",
    "react-responsive-carousel": "^3.2.23",
    "react-router-dom": "^5.1.2",
    "react-scripts": "^0.9.5",
    "react-select": "^5.2.2",
    "react-simple-flowchart": "^1.2.2",
    "react-table": "^7.7.0",
    "react-xml-viewer": "^1.3.0",
    "reactstrap": "^8.1.1",
    "styled-components": "^4.3.2",
    "xlsx-js-style": "^1.2.0",
    "xlsx.js": "^2.3.1",
    "xml-beautifier": "^0.4.3",
    "xml-formatter": "^1.2.0",
    "xml2js": "^0.4.23"
  },
  "jest": {
    "snapshotSerializers": [
      "enzyme-to-json/serializer"
    ]
  },
  "devDependencies": {
    "babel-plugin-dynamic-import-node": "^2.3.0",
    "enzyme": "^3.11.0",
    "enzyme-adapter-react-16": "^1.15.6",
    "enzyme-to-json": "^3.6.2"
  },
  "scripts": {
    "start": "set PORT=3000 && react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom --watch --testURL http://localhost/dashboard-ui",
    "eject": "react-scripts eject"
  },
  "homepage": ".",
  "browserslist": [
    ">0.2%",
    "not ie <= 11",
    "not op_mini all"
  ],
  "plugins": [
    "syntax-dynamic-import"
  ]
}

