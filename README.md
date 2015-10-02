# ImageUnderstanding

#added a new route decorator for returning JSON 
@app.route('/classify_upload_json', methods=['POST'])
def classify_upload_json():
    try:
        # We will save the file to disk for possible data collection.
        logging.info('In classify upload...')
        imagefile = flask.request.files['imagefile']
        filename_ = str(datetime.datetime.now()).replace(' ', '_') + \
            werkzeug.secure_filename(imagefile.filename)
        filename = os.path.join(UPLOAD_FOLDER, filename_)
        imagefile.save(filename)
        logging.info('Saving to %s.', filename)
        image = exifutil.open_oriented_im(filename)

    except Exception as err:
        logging.info('Uploaded image open error: %s', err)
        return flask.render_template(
            'index.html', has_result=True,
            result=(False, 'Cannot open uploaded image.')
        )

    result = app.clf.classify_image(image)
    
    #done with image file; no need to keep it around

    #os.remove(os.path.join(UPLOAD_FOLDER, filename))
    #logging.info('Deleted file with name, %s', (os.path.join(UPLOAD_FOLDER, filename)))

    return jsonify(result=result)
    # return flask.render_template(
    #     'index.html', has_result=True, result=result,
    #     imagesrc=embed_image_html(image)
