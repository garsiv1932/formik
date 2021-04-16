## Welcome to GitHub Pages

La creación de formularios en el desarrollo front end tiene sus complicaciones.Hay muchos factores que considerar: cambios de los campos, validaciones, submit...

Poir esto mismo, en React se ha popularizado la librería Formik para la gestión de formularios, junto con YUP para las validaciones, nos proporcionan las herramientas para generar formularios de una manera rápida, sencilla y eficiente.

Alos bifes

Lo primero, instalar FORMIK y YUP:
(Esto mismo lo pedes hacer con Yarn)

```
npm install formik --save
npm install -S yup
```
(Simplemente dos formas distintas de hacer lo mismo).

Aca les voy a mostrar el codigo de mi Landing Page:

### Markdown

Formik tiene 3 "momentos" muy importantes en el codigo, el primero es la definicion de los valores iniciales que van a tener los campos de nuestro form:
```
const initialValues={
        nombre: "",
        email:"",
        message: "",
        asunto: ""
    };
```

El segundo es la creacion del objeto Formik:
```
    const formik = useFormik({
      initialValues,
      onSubmit,
      validationSchema  
    });
```

Aqui tenemos, por un lado los valores iniciales, la funcion que se ejecutara en el submit del form (onSubmit), y el esquema de validacion, aqui es cuando entra el tercer "momento" mas importante de nuestro formulario, y que consta en la creacion de la logica de validacion de los campos.

***Lo primero que tengo que avisar, que parecera obvio para quienes ya sepan React, YUP matchea con el nomnbre del input, por lo que las claves del objeto YUP deben ser las mismas que en nuestro formualrio.

```
const validationSchema= Yup.object({
        email: Yup.string()
            .email('Mail invalido')
            .required('Reuqerido'),
        nombre: Yup.string()
            .max(200,'El nombre con 200 caracteres maximo')
            .required('Requerido'),
        message: Yup.string()
            .required('El mensaje no puede estar vacio')
    });
```

Si estuviesemos haciendo un formulario de registro con confirmacion de contraseña, la comparacion de passwords se hace de la siguiente manera:
```
        password: Yup.string()
            .required('Debe ingresar una contraseña')
            .min(8, 'Debe Contener al menos 8 caracteres')
            ,
        passwordConfirmation: Yup.string()
            .when("password", {
            is: val => (val && val.length > 0 ),
            then: Yup.string().oneOf(
                [Yup.ref("password")],
                "Las contraseñas deben coincidir"
            )
        })
            .required('Debe reingresar la contraseña')
```
Esta vsalidacion lo que hace es, en primer lugar verificar que el campo password sea valido, validando que este campo no es vacio, y posteriormente comparandolo con el campo password. Por ultimo colocamos que el campo es requerido, ya que si no la contraseña se verifica incluso cuando estamos escribiendo el password y no el passwordconfirmation. Todo esto tiene que ser completado con un operador ternario en el retorno HTML debajo del input del passwordConfirmation.

```
        { formik.touched.passwordConfirmation && formik.errors.passwordConfirmation ? (
            <div className='alert-danger bordiado'>{formik.errors.passwordConfirmation}</div>
        ) : null}
```

Una vez creado el objeto formik con sus validaciones y sus valores iniciales no queda mas que llamarlo desde la configuracion de los inputs del html desde el retorno de nuestro componente.

```
    <div className="col-sm-3 col-lg-3 col-12 mt-sm-0 mt-lg-0 mt-2">
        <input 
            type="text" 
            name="nombre" 
            placeholder="Nombre" 
            className={formik.touched.nombre && formik.errors.nombre
                ? "form-control is-invalid"
                : "form-control"
            }
            onChange={formik.handleChange}
            onBlur={formik.handleBlur}
            value={formik.values.nombre}
        />
    </div>
```



Por si te hace falta o preferis ver el codigo completo, aca lo dejo:

```
import './Contact.css'
import React, {useEffect, useState} from 'react';
import {useFormik} from 'formik';
import * as Yup from 'yup'
import emailjs from 'emailjs-com';
import  ReCaptcha  from  'google-recaptcha-react-component';

function Contact(){
    
    function recaptchaLoaded(){
        setCatpcha(true);
        console.log("cargo la concha de la lora");
    }
    
    const [captcha, setCatpcha] = useState(false);
    const [result, setResult] = useState("");
    
    
    const initialValues={
        nombre: "",
        email:"",
        message: "",
        asunto: ""
    };

    const validationSchema= Yup.object({
        email: Yup.string()
            .email('Mail invalido')
            .required('Reuqerido'),
        nombre: Yup.string()
            .max(200,'El nombre con 200 caracteres maximo')
            .required('Requerido'),
        message: Yup.string()
            .required('El mensaje no puede estar vacio')
    });

    const formik = useFormik({
      initialValues,
      onSubmit,
      validationSchema  
    });
    
    function onSubmit(values){
        debugger;
        // values.preventDefault();

        emailjs.send('LlorachDevs', 'template_j6yxg58', values, 'user_q0voPPOe8Y8ioYfA6BlPV')
            .then((result) => {
                setResult(result.text)
                console.log(result.text);
            }, (error) => {
                console.log(error.text);
                setResult(error.text)

            });
        }

    return(
        <div className="container-fluid mt-5 mb-5">
            <div className="row justify-content-center">
                <div className="col-sm-6 col-lg-6 col-12 text-center centerdiv">
                    <h1>Contacto</h1>
                    <p>Americo Vespucio 1445, Montevideo, 11700</p>
                    <p>llorach.pablo@llorachdevs.com</p>
                    <p>+59891211845</p>
                </div>
    
                <div className="col-sm-6 col-lg-6 col-12 ">
                    <form onSubmit={formik.handleSubmit}>
                        <div className="row justify-content-center">
                            <div className="col-sm-3 col-lg-3 col-12 mt-sm-0 mt-lg-0 mt-2">
                                <input 
                                    type="text" 
                                    name="nombre" 
                                    placeholder="Nombre" 
                                    className={formik.touched.nombre && formik.errors.nombre
                                        ? "form-control is-invalid"
                                        : "form-control"
                                    }
                                    onChange={formik.handleChange}
                                    onBlur={formik.handleBlur}
                                    value={formik.values.nombre}
                                />
                            </div>
                                
                            <div className="col-sm-3 col-lg-3 col-12 mt-sm-0 mt-lg-0 mt-2">
                                <input type="text" name="telefono" placeholder="Telefono" className="form-control"/>
                            </div>
                        </div>
                        <div className="row justify-content-center mt-2">
                            <div className="col-sm-6 col-lg-6 col-12">
                                <input 
                                    type="email" 
                                    name="email" 
                                    id="email" 
                                    placeholder="Email" 
                                    className={formik.touched.email && formik.errors.email
                                ? "form-control is-invalid" : "form-control"}
                                    onChange={formik.handleChange}
                                    onBlur={formik.handleBlur}
                                    value={formik.values.email}
                                />
                            </div>
                        </div>

                        <div className="row justify-content-center mt-2">
                            <div className="col-sm-6 col-lg-6 col-12">
                                <input 
                                    type="text" 
                                    name="asunto" 
                                    placeholder="Asunto" 
                                    className="form-control"
                                    onChange={formik.handleChange}
                                    onBlur={formik.handleBlur}
                                    value={formik.values.asunto}
                                />
                            </div>
                        </div>

    
                        <div className="row justify-content-center mt-2">
                            <div className="col-sm-6 col-lg-6 col-12">
                                <textarea
                                    className= { formik.touched.message && formik.errors.message
                                            ? "form-control is-invalid"
                                            : "form-control"
                                    }
                                    name="message"
                                    rows="4"
                                    placeholder="Escriba su mensaje aqui..."
                                    onChange={formik.handleChange}
                                    onBlur={formik.handleBlur}
                                    value={formik.values.message}
                                />
                                <div 
                                    className={(formik.touched.message && formik.errors.message) || (formik.touched.email && formik.errors.email) || (formik.touched.nombre && formik.errors.nombre)
                                    ?
                                    "error-messages alert-danger"
                                    :
                                    "error-messages"
                                    }
                                >
                                    
                                { (formik.touched.message && formik.errors.message) 
                                    ?
                                    <div className="bordiado ">{formik.errors.message}</div>
                                    :
                                    <div className="bordiado"></div>
                                }
                                
                                { (formik.touched.email && formik.errors.email)
                                    ? 
                                    <div className="bordiado">{formik.errors.email}</div>
                                    : 
                                    <div className="bordiado"></div>
                                }
                                
                                { (formik.touched.nombre && formik.errors.nombre)
                                ?
                                <div className="bordiado">{formik.errors.nombre}</div>
                                : 
                                <div className="bordiado"></div>
                                }
                                </div>
                            </div>
                        </div>
                        <div className="row justify-content-center">
                            <div className="col-sm-6 col-lg-6 col-12">
                                <button 
                                    type="submit" 
                                    className="btn btn-dark btn-block"
                                    disabled=
                                    {
                                        !formik.errors.message && !formik.errors.email && !formik.errors.nombre && captcha 
                                            ? null 
                                            : 
                                            "disabled"
                                    }
                                >Enviar</button>
                            </div>
                        </div>
                        { result !== ""
                            ?
                            <div className="row justify-content-center">
                                <div className="col-sm-6 col-lg-6 col-12">
                                    <p className="text-danger alert-danger bordiado mt-1">{result}</p>
                                </div>
                            </div>
                            :null
                        }
                    </form>
                    <div className="row justify-content-center">
                        <div className="col-sm-6 col-lg-6 col-12">
                            <ReCaptcha
                                token='XXXXXXXXXXXXXXXXXXXXX'
                                onSuccess={recaptchaLoaded}
                            />
                        </div>
                    </div>
                </div>
            </div>
        </div>
    )
}

```




For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Referencias



Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/garsiv1932/formik/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
