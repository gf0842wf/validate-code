验证码
========

Usage(Flask)
--

    from validate import create_validate_code
    import StringIO
        
    @app.route('/validcode', methods=['GET'])
    def validcode():
        code_img, strs = create_validate_code(font_type='font.ttf')) 
        buf = StringIO.StringIO()
        code_img.save(buf, 'JPEG', quality=70)
        buf_value = buf.getvalue()
        session['validcode'] = strs # 把验证码值存在session中
        response = make_response(buf_value)
        response.headers['Content-Type'] = 'image/jpeg'
        return response
        
    @app.route('/login', methods=['POST', 'GET'])
    def login():
        error = ''
        if request.method == "POST":
            phone = request.form['phone']
            password = request.form['password']
            try:
                cvalidcode = request.form['validcode'].upper()
                svalidcode = session.pop('validcode').upper()
                if cvalidcode != svalidcode:
                    error = u'验证码不正确!'
            except:
                logger.warn('valid except', exc_info=1)
                
            # 认证..
            
        session.pop('validcode', None)
        
        return render_template('login.html', error=error)
