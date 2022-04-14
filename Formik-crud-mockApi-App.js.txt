import React from 'react';
import axios from 'axios';
import AppBar from '@mui/material/AppBar';
import Box from '@mui/material/Box';
import Toolbar from '@mui/material/Toolbar';
import Button from '@mui/material/Button';
import { Form, Table } from 'react-bootstrap';
import 'bootstrap/dist/css/bootstrap.min.css';
import { Grid, Select, Item } from '@mui/material';
import { Formik } from 'formik';
import FormControl, { useFormControl } from '@mui/material/FormControl';
import OutlinedInput from '@mui/material/OutlinedInput';
import './index.css';


class CRUD extends React.Component {

  constructor() {
    super();
    this.state = {
      Product: [],
      ProductName: '',
      Productcompany: '',
      Productprice: '',
      id: '',
    }
  }

  async componentDidMount() {
    var response = await axios.get('https://62530f93c534af46cb933fa1.mockapi.io/Product/Products')
    await this.setState({ Product: response.data })
  }

  validate = (formData) => {
    var errors = {};
    if (formData.ProductName == '') errors.ProductName = 'ProductName is Required';
    if (formData.Productcompany == '') errors.Productcompany = 'Productcompany is Required';
    if (formData.Productprice == '') errors.Productprice = 'Productprice is Required';
    if (formData.Gender == '') errors.Gender = 'Gender is Required';
    return errors;
  };

  submit = async (formData) => {

    if (this.state.id) {
      var response = await axios.put(`https://62530f93c534af46cb933fa1.mockapi.io/Product/Products/${this.state.id}`,
        {
          ProductName: formData.ProductName,
          Productcompany: formData.Productcompany,
          Productprice: formData.Productprice
        })
      var index = this.state.Product.findIndex((row) => row.id === this.state.id)
      var Product = [...this.state.Product]
      Product[index] = response.data
      this.setState({ Product, ProductName: '', Productcompany: '', Productprice: '' })
    }
    else {
      var post = await axios.post('https://62530f93c534af46cb933fa1.mockapi.io/Product/Products', {
        ProductName: formData.ProductName,
        Productcompany: formData.Productcompany,
        Productprice: formData.Productprice
      })

      var Product = [...this.state.Product];
      Product.push(post.data);
      this.setState({ Product, ProductName: '', Productcompany: '', Productprice: '' });
    }
  }



  render() {

    const Update = async (id) => {
      var selectedData = await this.state.Product.filter((row) => row.id == id)[0]
      await this.setState({
        ProductName: selectedData.ProductName,
        Productcompany: selectedData.Productcompany,
        Productprice: selectedData.Productprice,
        id: selectedData.id
      })
    }

    const Delete = (id) => {
      var response = axios.delete(`https://62530f93c534af46cb933fa1.mockapi.io/Product/Products/${id}`)
      var Product = this.state.Product.filter((row) => row.id !== id)
      this.setState({ Product })
    }




    return (
      <>
        <div >
          <Box sx={{ flexGrow: 1 }}>
            <AppBar position="static">
              <Toolbar >
                <span className="Box">Product Creation Using Formik With CRUD Using Mockapi</span>
              </Toolbar>
            </AppBar>
          </Box>
        </div>

        <Formik
          enableReinitialize
          initialValues={this.state}
          validate={(formData) => this.validate(formData)}
          onSubmit={(formData) => this.submit(formData)}
        >
          {({
            values,
            errors,
            touched,
            handleChange,
            handleBlur,
            handleSubmit,
            isSubmitting,
          }) => (
            <form className='form' onSubmit={handleSubmit}>
              <div >
                <label>Product Name</label> &nbsp;
                <div className='input1' style={{ display: 'inline-block' }}>
                  <Box component="form" noValidate autoComplete="off">
                    <FormControl sx={{ width: '35ch' }}>
                      <OutlinedInput placeholder="Please enter ProductName" type="text"
                        name="ProductName"
                        value={values.ProductName}
                        onChange={handleChange}
                        onBlur={handleBlur}
                        className='input1' />
                    </FormControl>
                  </Box>
                </div>
                <br />
                <span style={{ color: 'red' }}>
                  {touched.ProductName && errors.ProductName}
                </span>
              </div>
              <br></br>
              <div>
                <label>Product Company</label> &nbsp;
                <div className='input2' style={{ display: 'inline-block' }}>
                  <Box component="form" noValidate autoComplete="off">
                    <FormControl sx={{ width: '35ch' }}>
                      <OutlinedInput placeholder="Please enter Productcompany" type="text"
                        name="Productcompany"
                        value={values.Productcompany}
                        onChange={handleChange}
                        onBlur={handleBlur}
                        className='input2' />
                    </FormControl>
                  </Box>
                </div>
                <br />
                <span style={{ color: 'red' }}>
                  {touched.Productcompany && errors.Productcompany}
                </span>
              </div>
              <br></br>
              <div>
                <label>Product Price</label> &nbsp;
                <div className='input3' style={{ display: 'inline-block' }}>
                  <Box component="form" noValidate autoComplete="off">
                    <FormControl sx={{ width: '35ch' }}>
                      <OutlinedInput placeholder="Please enter Productprice" type="text"
                        name="Productprice"
                        value={values.Productprice}
                        onChange={handleChange}
                        onBlur={handleBlur}
                        className='input3' />
                    </FormControl>
                  </Box>
                </div>
                <br />
                <span style={{ color: 'red' }}>
                  {touched.Productprice && errors.Productprice}
                </span>
              </div>
              <br></br>

              <div className='Submitbutton'>
                <Button type="submit" variant="contained" disabled={isSubmitting} >submit</Button> &nbsp;
              </div>
              <br></br>
            </form>
          )}
        </Formik>
        <div className="grid">
          <Grid container spacing={2}>
            <Grid xs={8}>
              <Table striped bordered hover variant="primary" border='1'>
                <thead>
                  <tr>
                    <td> id </td>
                    <td> ProductName </td>
                    <td> Productcompany </td>
                    <td> Productprice </td>
                    <td> Actions </td>
                  </tr>
                </thead>
                <tbody>
                  {this.state.Product.map((row) => (
                    <tr>
                      <td> {row.id} </td>
                      <td> {row.ProductName} </td>
                      <td> {row.Productcompany} </td>
                      <td> {row.Productprice} </td>
                      <td> <Button variant="contained" onClick={() => Update(row.id)} >Edit</Button> &nbsp; &nbsp; &nbsp;
                        <Button variant="contained" onClick={() => Delete(row.id)} >Delete</Button></td>
                    </tr>
                  ))}
                </tbody>
              </Table>
            </Grid>
          </Grid>
        </div>
      </>
    );
  }
}

export default CRUD
