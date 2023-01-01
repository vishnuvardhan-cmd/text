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
